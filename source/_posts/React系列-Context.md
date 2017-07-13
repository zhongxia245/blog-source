---
title: React系列->Context
date: 2016-05-05 10:29:16
tags: React
categories: React
---
Contexts 是React的一个重要属性，但是到目前为止，这个属性在正式的文档里面还没有对它进行正式介绍，在 reactv0.1.4将会正式发布这个属性。下面先来介绍一下它的使用方式。
Contexts 可以把父组件的的数据,方法传给子组件使用. 而不用每一次都使用给子组件赋值属性的方式,传递方式.
原文地址: [React context](https://segmentfault.com/a/1190000002878442)
<!--more-->

---

### React.withContext
会执行一个指定的上下文信息的回调函数,任何在这个回调函数里面渲染的组件都有这个context的访问权限。

```
var A = React.createClass({
    contextTypes: {
        name: React.PropTypes.string.isRequired,
    },
    render: function() {
        return < div > My name is: {
            this.context.name
        } < /div>;
    }
});
React.withContext({'name': 'Jonas'}, function () {
    // Outputs: "My name is: Jonas"
React.render( < A / >, document.body);
    });
```
任何想访问context里面的属性的组件都必须显式的指定一个contextTypes
 的属性。如果没有指定改属性，那么组件通过 this.context 访问属性将会出错。
如果你为一个组件指定了context，那么这个组件的子组件只要定义了contextTypes 属性，就可以访问到父组件指定的context了。

```
var A = React.createClass({
    render: function() {
        return < B / >;
    }
});
var B = React.createClass({
    contextTypes: {
        name: React.PropTypes.string
    },
    render: function() {
        return < div > My name is: {
            this.context.name
        } < /div>; }});React.withContext({'name': 'Jonas'}, function () { React.render(<A / > ,
        document.body);
    });
```
为了减少文件的引用，你可以为contextTypes放到一个mixin 中，这样 用到的组件引用这个 mixin 就行了。
```
var ContextMixin = {
    contextTypes: {
        name: React.PropTypes.string.isRequired
    },
    getName: function() {
        return this.context.name;
    }
};
var A = React.createClass({
    mixins: [ContextMixin],
    render: function() {
        return < div > My name is {
            this.getName()
        } < /div>; }});React.withContext({'name': 'Jonas'}, function () { / / Outputs: "My name is: Jonas"React.render( < A / >, document.body);
    });
```

### getChildContext
和访问context 的属性是需要通过 contextTypes指定可访问的 元素一样。getChildContext指定的传递给子组件的属性需要先通过 childContextTypes指定，不然会产生错误。
```
// This code *does NOT work* becasue of a missing property from childContextTypes
var A = React.createClass({

    childContextTypes: {
        // fruit is not specified, and so it will not be sent to the children of A
        name: React.PropTypes.string.isRequired
    },

    getChildContext: function() {
        return {
            name: "Jonas",
            fruit: "Banana"
        };
    },

    render: function() {
        return < B / >;
    }
});

var B = React.createClass({

    contextTypes: {
        fruit: React.PropTypes.string.isRequired
    },

    render: function() {
        return < div > My favorite fruit is: {
            this.context.fruit
        } < /div>;
    }
});


/ / Errors: Invariant Violation: A.getChildContext() : key "fruit"is not defined in childContextTypes.React.render( < A / >, document.body);
```


假设你的应用程序有多层的context。通过withContext和 getChildContext指定的context元素都可以被子组件引用。但是子组件是需要通过 contextTypes来指定所需要的context 元素的。

```
var A = React.createClass({
    childContextTypes: {
        fruit: React.PropTypes.string.isRequired
    },
    getChildContext: function() {
        return {
            fruit: "Banana"
        };
    },
    render: function() {
        return < B / >;
    }
});
var B = React.createClass({
    contextTypes: {
        name: React.PropTypes.string.isRequired,
        fruit: React.PropTypes.string.isRequired
    },
    render: function() {
        return < div > My name is: {
            this.context.name
        }
        and my favorite fruit is: {
            this.context.fruit
        } < /div>; }});React.withContext({'name': 'Jonas'}, function () { / / Outputs: "My name is: Jonas and my favorite fruit is: Banana"React.render( < A / >, document.body);
    });
```

context 是就近引用的，如果你通过withContext指定了context元素，然后又通过 getChildContext指定了该元素，该元素的值将会被覆盖。
```
var A = React.createClass({
    childContextTypes: {
        name: React.PropTypes.string.isRequired
    },
    getChildContext: function() {
        return {
            name: "Sally"
        };
    },
    render: function() {
        return < B / >;
    }
});
var B = React.createClass({
    contextTypes: {
        name: React.PropTypes.string.isRequired
    },
    render: function() {
        return < div > My name is: {
            this.context.name
        } < /div>; }});React.withContext({'name': 'Jonas'}, function () { / / Outputs: "My name is: Sally"React.render( < A / >, document.body);
    });
```
## 总结
通过context传递属性的方式可以大量减少 通过显式的通过 props逐层传递属性的方式。这样可以减少组件之间的直接依赖关系。