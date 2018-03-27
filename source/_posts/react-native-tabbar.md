---
title: React-Native 开发篇一底部导航菜单
date: 2018-03-27 12:54:20
tags: react-native
categories: 前端
---

接着上一篇【[React-Native 开发篇一React-Native+Redux开发框架搭建](https://wangheng3751.github.io/2018/02/02/react-native-redux-demo/)】，本文记录使用react-native-router-flux组件实现rn开发时最常见的底部导航菜单！

实现效果如下图：

![效果预览](https://upload-images.jianshu.io/upload_images/9814928-5a4054b08e3482ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 1.组件安装
>npm install react-native-router-flux --save

### 2.定义菜单图标及文字（tabIcon.js）
```
import React, { Component } from 'react';
import { View, Image, Text, StyleSheet,Dimensions } from 'react-native';
class TabIcon extends Component {
    constructor(props){
        super(props);       
    }

    render(){
        let selected=this.props.focused;
        let data={
            home:{
                title:"首页",
                icon:!selected?require("../resource/images/home.png"):require("../resource/images/home_selected.png")
            },
            movies:{
                title:"电影",
                icon:!selected?require("../resource/images/movies.png"):require("../resource/images/movies_selected.png")
            },
            theaters:{
                title:"影院",
                icon:!selected?require("../resource/images/theater.png"):require("../resource/images/theater_selected.png")
            },
            me:{
                title:"我",
                icon:!selected?require("../resource/images/me.png"):require("../resource/images/me_selected.png")
            }
      }
      let param=data[this.props.navigation.state.key];
      return  <View style={styles.tabbarContainer}>
                <Image style={{ width: 25, height: 25,resizeMode:'contain' }} source={param.icon} />
                <Text style={[styles.tabbarItem,selected&&{color:'#F08519'}]}>{param.title}</Text>
              </View>
    }
}

const styles = StyleSheet.create({
    tabbarContainer:{
      flex:1,
      alignItems:"center",
      justifyContent:"center",
      width:Dimensions.get('window').width/4
    },
    tabbarItem:{  
      marginTop:5,    
      textAlign:"center"
    }
});

module.exports = TabIcon;
```
注意事项：
- 定义菜单标题和图标的data数据的key（即例子中的home/movies/theaters/me）和步骤3中每一个Scene的key一一对应；
- 判断菜单是否被选中this.props.focused在老版本的react-native-router-flux使用this.props.selected;
- 取当前菜单this.props.navigation.state.key在老版本的react-native-router-flux使用this.props.sceneKey。
### 3.定义底部导航栏（appRoot.js）
```
import React, { Component } from 'react';
import { Provider } from 'react-redux';
import PropTypes from 'prop-types';
import {
    View,
    Text,
    BackAndroid,
    StyleSheet
} from 'react-native';
import { Scene, Router, TabBar, Modal, Schema, Actions, Reducer, ActionConst } from 'react-native-router-flux';
import { connect } from 'react-redux';
import LoginPage from './modules/auth/containers/loginPage';
import HomeIndex from './modules/home/containers/indexPage';
import TabIcon from './common/tabIcon';

class AppRoot extends Component {
    static propTypes = {
        dispatch: PropTypes.func
    }

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
                    createReducer={ this.createReducer.bind(this) }
                    scenes={ scenes }
             >          
            </Router >
        )
    }    
}

const styles = StyleSheet.create({
    tabBarStyle: {
        backgroundColor: '#fff',
        height:64
    },
    tabBarSelectedItemStyle: {
        backgroundColor: '#fff'
    },
    titleStyle: {
        color: '#fff'
    },
})

const scenes = Actions.create(
    <Scene key="root" hideNavBar={true}>
        <Scene key="login" component={LoginPage} title="登录" hideNavBar={true} />
        <Scene key="tabbar"
                initial
                tabs={true}
                tabBarPosition="bottom"
                showLabel={false}
                tabBarStyle={styles.tabBarStyle}
                tabBarSelectedItemStyle={styles.tabBarSelectedItemStyle}
                titleStyle={styles.titleStyle}>
                <Scene key="home"
                    hideNavBar={true}
                    component={HomeIndex}
                    icon={TabIcon}
                    titleStyle={styles.titleStyle}/>

                <Scene key="movies"
                    hideNavBar={true}
                    component={HomeIndex}      
                    icon={TabIcon}                
                    titleStyle={styles.titleStyle} />

                <Scene key="theaters"
                    hideNavBar={true}
                    component={HomeIndex}                           
                    icon={TabIcon}
                    titleStyle={styles.titleStyle} />

                <Scene key="me"
                    hideNavBar={true}
                    component={LoginPage}                    
                    icon={TabIcon}
                    titleStyle={styles.titleStyle} />
            </Scene>
    </Scene>
)
export default connect()(AppRoot);
```
注意事项：
- 引入步骤2中定义的tabIcon(import TabIcon from './common/tabIcon')；
- 定义一个Scene使其initial={true}  tabs={true}作为底部菜单容器；
- 定义若干（一舨2-4个）个菜单项，使其key与tabIcon中的key相对于，设置icon={TabIcon}, component为该菜单选中时默认的组件。

此外，由于此示例基于redux，为完整项目结构，还需做以下处理：

- 定义dispatch
```
import PropTypes from 'prop-types';
...
class AppRoot extends Component {
  static propTypes = {
        dispatch: PropTypes.func
   }
...
}
```

- 使用connect连接React组件
```
export default connect()(AppRoot);
```

---
GIT源码地址：[react-native-demo](https://github.com/wangheng3751/react-native-demo)     分支名称：tabbar
