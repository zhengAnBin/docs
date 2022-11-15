## 前言-什么是 JSX

```jsx
<article>
    <h1>My First Component</h1>
    <ol>
        <li>Components: UI Building Blocks</li>
        <li>Defining a Component</li>
        <li>Using a Component</li>
    </ol>
</article>

// JSX 在第一视觉会让人认为是 HTML

/**
 * 来自官网的一句话:
 * JSX 是 JavaScript 的一种语法扩展，它和模版语言很接近，但是它充分具备 JavaScript 的能力
 */
```

### JavaScript 是如何扩展出 JSX

[在线工具](https://www.babeljs.cn/repl#?browsers=defaults%2C%20not%20ie%2011%2C%20not%20ie_mob%2011&build=&builtIns=false&corejs=3.6&spec=false&loose=false&code_lz=Q&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=env%2Creact%2Cstage-2&prettier=false&targets=&version=7.17.2&externalPlugins=&assumptions=%7B%7D)

```
// 这得益于 Babel 语法转换工具中的一个插件 @babel/preset-react

<div className="box">
    <h1>title text</h1>
</div>

// 经过转换后

createElement("div", {
	className: "box"
}, createElement("h1", null, "title text"));

// 变成了一个名为 createElement 的函数调用
// 而这个 createElement 是 React 里的一个函数

```

### createElement

```jsx
// 版本：17.0.1

/**
 * type: 标识节点类型。它可以是 "h1"、 "div" 这些 HTML 标签字符串，也可以是 React 组件
 * config: 节点上的属性，以键值对的形式存储。
 * children: 节点之间的嵌套内容
 */
function createElement(type, config, children) {
    let propName;

    const props = {};

    let key = null;
    let ref = null;
    let self = null;
    let source = null;

    if (config != null) {
        // 依次对 ref, key, self 和 source 属性赋值
        if (hasValidRef(config)) {
            ref = config.ref;
        }

        if (hasValidKef(config)) {
            key = "" + config.key;
        }
        self = config.__self === undefined ? null : config.__self;
        source = config.__source === undefined ? null : config.__source;

        for (propName in config) {
            if (
                // 筛选出可以提进 props 对象里的属性
                hasOwnProperty.call(config, propName) &&
                !RESERVED_PROPS.hasOwnProperty(propName)
            ) {
                props[propName] = config[propName];
            }
        }
    }

    // childrenLength 指的是当前元素的子元素的个数，减去的 2 是 type 和 config 两个参数占用的长度
    const childrenLength = arguments.length - 2;

    if (childrenLength === 1) {
        props.children = children;
    } else if (childrenLength > 1) {
        const childArray = Array(chlidrenLength);

        for (let i = 0; i < childrenLength; i++) {
            childArray[i] = arguments[i + 2];
        }

        props.children = childArray;
    }

    if (type && type.defaultProps) {
        const defaultProps = type.default;
        for (propName in defaultProps) {
            if (props[propName] === undefined) {
                props[propName] = defaultProps[propName];
            }
        }
    }

    return ReactElement(type, key, ref, undefined, undefined, ReactCurrentOwner.current, props);
}

// 最后调用了 ReactElement 函数
```

### ReactElement

ReactElement 的代码非常简单，它没有对 createElement 函数所返回的结果做值的改变。只是整合了一下参数，然后返回整合后的结果

站点类型的角度来看，它定义了 React 元素的类型结构，ReactElement 只是该类型的一个标识符

而这个函数调用后返回的结果，就是 `虚拟DOM`

### JSX 给我们带来的好处

-   对比转换前的语法和转换后的语法，JSX 带来的体验会更加舒适、且效率更高

-   跨平台。理论上，我只需根据当前的设备，写出适配设备页面的渲染器，就可以将 JSX 可视化给用户。那么 JSX 就可以是 web 界面，也可以是 IOS 界面，安卓界面，小程序

-   ~~在某些场景下可以提高性能（差量更新）~~

JSX 只是一种语法糖，它对可视化的界面抽象为 JavaScript

的普通对象(object)。而将其渲染为用户界面还需要 ReactDOM 来完成
