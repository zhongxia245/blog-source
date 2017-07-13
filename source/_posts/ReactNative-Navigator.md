---
title: ReactNative Navigator
date: 2016-05-01 00:08:28
tags: ReactNative
categories: ReactNative
---
移动App中,Navigator组件是最重要的几个组件之一. 虽然使用的方式无非就那么几种,但是却是不可或缺的.
<!--more-->

---
## 如何使用?
---
### 1. 手动匹配对应的页面
1. 引用Navigator 组件
```
const React = require('react-native');
const {
  AppRegistry,
  View,
  Navigator,
  Text,
  BackAndroid,
  StyleSheet
} = React;
```

2. 在入口app 文件使用 Navigator 组件
```
render() {
    return (
      <Navigator
        initialRoute={{name: 'welcome'}}
        configureScene={this.configureScene}
        renderScene={this.renderScene} />
    );
  }
```
initialRoute: 默认状态,可根据该状态匹配不同的页面
configureScene: 页面跳转的动画效果  -->[效果列表](http://reactnative.cn/docs/0.24/navigator.html#content)
renderScene: 页面匹配规则
```
  /**
   * 切换界面的效果
   */
  configureScene(route) {
    return Navigator.SceneConfigs.FloatFromRight; //默认, 从右边滑动
    //return Navigator.SceneConfigs.FadeAndroid;  //淡入淡出
  },
  /**
   * 渲染什么页面
   */
  renderScene(router, navigator) {
    var Component = null;

    this._navigator = navigator;
    switch (router.name) {
      case "welcome":
        Component = WelcomeView;  //某一个页面
        break;
      case "feed":
        Component = FeedView;   //某一个页面
        break;
      default: //default view
        Component = DefaultView;  //某一个页面
    }

    return <Component navigator={navigator} />
  },
```

3. 已经配置好了,路由如何跟组件关联关系,那么如何进行点击跳转呢?
```
/*******************************************************
 * DefaultView Page [其他页面类似]
 *******************************************************/
const DefaultView = React.createClass({
  onPressFeed() {
    this.props.navigator.push({ name: 'welcome' })  //点击设置路由跳转
  },
  render() {
    return (
      <View style={styles.container}>
          <Text style={styles.welcome} onPress={this.onPressFeed}>Default view</Text>
      </View>
    )
  }
});
```

---
### 2. 自动匹配对应的页面 [ 待完善 ]




---
## 完整的代码
### 1. 手动匹配 [完整代码] 
```
'use strict';

const React = require('react-native');
const {
  AppRegistry,
  View,
  Navigator,
  Text,
  BackAndroid,
  StyleSheet
} = React;

/*******************************************************
 * FeedView Page
 *******************************************************/
const FeedView = React.createClass({
  goBack() {
    this.props.navigator.push({ name: "default" });
  },

  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.welcome} onPress={this.goBack} >
            I am Feed View! Tab to default view!
        </Text>
      </View>
    )
  }
});


/*******************************************************
 * WelcomeView Page
 *******************************************************/
const WelcomeView = React.createClass({
  onPressFeed() {
    this.props.navigator.push({ name: 'feed' });
  },


  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.welcome} onPress={this.onPressFeed} >
            This is welcome view.Tap to go to feed view.
        </Text>
      </View>
    );
  }

});

/*******************************************************
 * DefaultView Page
 *******************************************************/
const DefaultView = React.createClass({
  onPressFeed() {
    this.props.navigator.push({ name: 'welcome' })
  },
  render() {
    return (
      <View style={styles.container}>
          <Text style={styles.welcome} onPress={this.onPressFeed}>Default view</Text>
      </View>
    )
  }
});


/*******************************************************
 * SampleApp Main
 *******************************************************/
const SampleApp = React.createClass({

  /**
   * 切换界面的效果
   */
  configureScene(route) {
    return Navigator.SceneConfigs.FloatFromRight; //默认, 从右边滑动
    //return Navigator.SceneConfigs.FadeAndroid;  //淡入淡出
  },

  /**
   * 渲染什么页面
   */
  renderScene(router, navigator) {
    var Component = null;

    this._navigator = navigator;
    switch (router.name) {
      case "welcome":
        Component = WelcomeView;
        break;
      case "feed":
        Component = FeedView;
        break;
      default: //default view
        Component = DefaultView;
    }

    return <Component navigator={navigator} />
  },

  /**
   * 不写这个对IOS没有影响,目前没有发现影响
   * @return {[type]} [description]
   */
  componentDidMount() {
    var navigator = this._navigator;
    BackAndroid.addEventListener('hardwareBackPress', function() {
      if (navigator && navigator.getCurrentRoutes().length > 1) {
        navigator.pop();
        return true;
      }
      return false;
    });
  },


  componentWillUnmount() {
    BackAndroid.removeEventListener('hardwareBackPress');
  },

  render() {
    return (
      <Navigator
        initialRoute={{name: 'welcome'}}
        configureScene={this.configureScene}
        renderScene={this.renderScene} />
    );
  }

});



const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
  welcome: {
    fontSize: 30,
    textAlign: 'center',
    margin: 10,
  },
  instructions: {
    textAlign: 'center',
    color: '#333333',
    marginBottom: 5,
  },
});

AppRegistry.registerComponent('AwesomeProject', () => SampleApp);

```

