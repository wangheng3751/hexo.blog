---
title: React-Native 开发篇一React-Native+Redux开发框架搭建
date: 2018-02-02 17:01:55
categories: 前端
tags: React-Native
---

前言：从事react-native开发一年半的时间了，从最初的0.17版本开始接触react-native到现在的0.51稳定版，期间从一开始的看着官方文档自我摸索到后来的渐渐社区庞大，各种组件层出不穷，react-native虽然越发成熟，然而大版本的改动，新技术的更替往往让人措手不及，从一开始的单页面涵盖组件，状态以及后台交互代码，到后来的组件拆分，说不上那种更好，但往往我们习惯学习大部分人的想法，接触了一段时间的redux之后，远远不及精通，甚至有时候只是知其然而不知其所以然，然而生活总有新的东西要去做，最近要去搞vue了，避免新的东西取代大脑里面旧的东西，暂切在此记录以下react-native开发过程中的一些经验和总结，以便以后查阅。本文记录使用redux状态管理机制来配合react-native开发的一些过程。
### 一.项目初始化
react-native init reduxDemo

初始化完成后控制台输出如下信息：
![](http://upload-images.jianshu.io/upload_images/9814928-f66072ccd1f59094.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 二. 依赖安装
必须依赖项：
- npm install redux --save
- npm install react-redux --save
- npm install redux-thunk --save

扩展依赖：
- npm install immutable --save
- npm install react-native-router-flux --save
### 三.项目结构
![](http://upload-images.jianshu.io/upload_images/9814928-afcc8963d9f522b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- src-项目文件总文件夹。
-- src/modules-项目模块文件夹，按模块规划建立子文件夹，按每个模块划分项的state,reducer和action,(问题：按模块划分还是页面单独划分为好？)如登录授权模块为auth。
--- src/modules/auth-登录授权模块。
-- src/resource-整个项目资源文件，多为图片，问题：资源文件是放置到相应的模块文件夹下还是在此处统一管理，那种较好？不知道，大概各有各的好吧。
--src/actionTypes.js-整个项目操作定义。
--src/appState.js-整个项目状态统一管理。
--src/actionStore.js-state中间件。
--src/actionReducer.js-整个项目reducer 统一管理。
--src/actionRoot.js-项目路由管理。
### 四.核心代码
1. appState.js
```
import authState from './modules/auth/authState';

export default function getAppState () {
  const appState = {
    auth: new authState()
    //...其他模块State
  }
  return appState;
}
```
>备注：整个APP状态汇总，开发时定义在各个模块下，此处由各个模块文件引入做统一管理。

2.appTypes.js
```
export const LOGIN_SUCCESS 	= 'LOGIN_SUCCESS';//登录成功
export const LOGIN_FAILURE 	= 'LOGIN_FAILURE';//登录失败
```
>备注：reducer中更新状态使用。

3.appReducer.js
```
import auth from './modules/auth/authReducer';
import { combineReducers } from 'redux';

const appReducer = combineReducers({
    auth
    //...其他模块reducer
});
export default appReducer;
```
>备注：整个APP Reducer汇总，开发时定义在各个模块下，此处由各个模块文件引入做统一管理。

4.appStore.js
```
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import reducer from './appReducer';
import getAppState from './appState';

const createStoreWithMiddleware = applyMiddleware(thunk) (createStore);

function configureStore (initialState) {
    return createStoreWithMiddleware(reducer, initialState);
}
var appStore=configureStore(getAppState());
export default appStore;
```
5.appRoot.js
```
import React, { Component } from 'react';
import { Provider } from 'react-redux';
import {
    View,
    Text,
    BackAndroid
} from 'react-native';
import { Scene, Router, TabBar, Modal, Schema, Actions, Reducer, ActionConst } from 'react-native-router-flux';
import LoginPage from './modules/auth/containers/loginPage';

export default class AppRoot extends Component {
    constructor(props) {
        super(props);
    }

    createReducer(params) {
        const defaultReducer = Reducer(params);
        return (state, action) => {
          this.props.dispatch(action);
          return defaultReducer(state, action);
        };
    }

    onExitApp(){
        BackAndroid.exitApp();
        return true;
    }
    render() {
        return (
            <Router onExitApp={this.onExitApp} 
                    createReducer={ this.createReducer }
                    scenes={ scenes }
             >          
            </Router >
        )
    }    
}

const scenes = Actions.create(
    <Scene key="root" hideNavBar={true}>
        <Scene key="login" component={LoginPage} title="登录" hideNavBar={true} initial />
    </Scene>
)
```
>备注：APP路由管理页面。

6.App.js
```
import React, { Component } from 'react';
import { Provider } from 'react-redux';
import AppRoot from './src/appRoot';
import appStore from './src/appStore';

export default class App extends Component<{}> {
  constructor(props) {
    super(props);
  } 
  render() {
    return (
        <Provider store={appStore}>
            <AppRoot />
        </Provider>
    );
  }
}
```
>备注：APP入口。

### 五.示例模块
模块结构：
![项目结构](http://upload-images.jianshu.io/upload_images/9814928-f21665431efa3f25.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1.state定义
```
import { Record, List } from 'immutable';

let authState = Record({
    isLogin:false
});

export default authState;
```
2.action定义
```
import * as types from '../../actionTypes';

export function login(userName,password) {
    return {
        type:types.LOGIN_SUCCESS
    }  
}
```
3.reducer定义
```
import AuthState from './authState';
import * as types from '../../actionTypes';
import Immutable, { Record, List } from 'immutable';

const authState = new AuthState();
export default function authReducer(state = authState, action) {
    switch (action.type) {
        case types.LOGIN_SUCCESS:
             return state.set('isLogin', true);
        case types.LOGIN_FAILURE:
             return state.set('isLogin', false);
        default: 
             return state;
    }
    return state;
}
```
4.component定义
```
import React, { Component } from 'react';
import { StyleSheet, Platform, TextInput, View, Dimensions, Text,ImageBackground, Image, TouchableOpacity } from 'react-native';

export default class LoginComponent extends Component {
    constructor(props) {
        super(props);
    }

    render() {
        return (         
            <ImageBackground source={require('../../../resource/login-background.jpg')} resizeMode='stretch' style={[styles.stretch,styles.center]}>               
                <Image source={require('../../../resource/logo.png')} style={styles.logo}/>
                <TextInput placeholder='请输入您的用户名' style={styles.input}/>
                <TextInput placeholder='请输入您的密码' secureTextEntry={true} style={styles.input}/>
                <TouchableOpacity style={[styles.btn,styles.center]} onPress={this.props.onLogin}>
                    <Text style={styles.text}>登录</Text>
                </TouchableOpacity>
            </ImageBackground>
        )
    }  
}

var {height, width} = Dimensions.get('window');
const styles = StyleSheet.create({
    logo:{
        width:80,
        height:80,
        marginBottom:50
    },
    center:{
        alignItems: 'center',
        justifyContent:'center'
    },
    stretch: {
        height:height,
        width:width   
    },
    input:{
        height:30,
        width:width*0.6,
        padding:0,
        borderBottomWidth:1,
        borderBottomColor:"#8ecbc1",
        paddingLeft:20,
        marginBottom:10
    },
    btn:{
        width:width*0.6,
        backgroundColor:"#8ecbc1",
        height:32,
        marginTop:20
    },
    text:{
        color:"#fff",
        fontSize:16
    }
});

```
5.container定义
```
import React, { Component } from 'react';
import LoginComponent from "../components/loginComponent"
import { bindActionCreators } from 'redux';
import { connect } from 'react-redux';
import * as authActions from '../authActions';

class LoginPage extends Component {
    constructor(props) {
        super(props);
    }

    componentWillReceiveProps(nextProps,nextState){
        if(nextProps.isLogin){
            alert("登录成功");
        }
    }

    render() {
        return (
            <LoginComponent onLogin={this.props.actions.login}/>
        )
    }  
}

function mapStateToProps(state) {
    return {
        isLogin:state.auth.isLogin
    }
}

function mapDispatchToProps(dispatch) {
    return {
        actions: bindActionCreators(authActions, dispatch)
    }
}

export default connect(mapStateToProps, mapDispatchToProps)(LoginPage);
```

### 六.参考资料
- [redux中文文档](http://cn.redux.js.org/)
---
GIT源码地址：[react-native-demo](https://github.com/wangheng3751/react-native-demo)     分支名称：framework (git checkout framework)

