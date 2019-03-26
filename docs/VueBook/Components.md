# 组件细节问题

现在我们观察一些用框架开发的网页[BiliBili](https://www.bilibili.com)、[掘金](https://juejin.im/)，会发现很多部分都十分相似或者一模一样，我们甚至可以将其拆分归类。而事实上，页面的确是被一个个组件构成的，本章，我们就对组件的细节做一个细致的拆分。

## 组件的使用

组件的注册分为全局注册和局部注册，目前我们用全局注册做例子。

```js
Vue.component("Row", {
  // 组件中的data必须是个函数并返回组件的数据
  data() {
    return {
      content: "This is row"
    };
  },
  template: `<tr><td>{{content}}</td></tr>`
});
```

使用`Vue.component()`对 Vue 实例进行全局组件的注册。这里我们注册了一个叫 Row 的组件，本质是它的模板在`template`中定义。另外需要注意的是，与根实例 `vm` 不同，组件的 data 数据是一个函数，return 出绑定在组件上的属性。

### is 的使用

刚刚注册了一个全局的组件，现在我们来使用它，在页面上渲染一个表格，Row 的本质是`<tr><td>{{content}}</td></tr>`，我们用`<tbody></tbody>`包裹。

```html
<div id="app">
  <table>
    <tbody>
      <Row></Row>
      <Row></Row>
      <Row></Row>
    </tbody>
  </table>
</div>
```

走到页面，看到数据正常渲染，但真的是这样么？按 F12 打开控制台，我们审查元素，会惊奇的发现`<tbody></tbody>`并没有如我们所愿包裹着`<Row></Row>`组件渲染出来的`<tr><td>{{content}}</td></tr>`，而是在外面。其本质原因是因为在 H5 的规定中，`<table></table>`中只能放`<td></td>`和`<tr></tr>`。因此我们需要使用`is`属性来做一个转换。

```html
<div id="app">
  <!-- 使用is属性解决模板标签上不规范的bug -->
  <table>
    <tbody>
      <tr is="Row"></tr>
      <tr is="Row"></tr>
      <tr is="Row"></tr>
    </tbody>
  </table>
</div>
```

这里相当于做了一个翻译：用 is 属性说，这里用的是 tr，但 tr 实际是 Row 组件。
::: warning
在 Vue 中，使用组件的时候，难免会遇到不合语义的写法，应该尽量避免。
:::

## 全局注册于局部注册

到目前为止，我们只用过 `Vue.component` 来创建组件

```js
Vue.component("my-component-name", {
  // ... 选项 ...
});
```

这些组件是全局注册的。也就是说它们在注册之后可以用在任何新创建的 Vue 根实例 (new Vue) 的模板中。比如：

```js
Vue.component("component-a", {
  /* ... */
});
Vue.component("component-b", {
  /* ... */
});
Vue.component("component-c", {
  /* ... */
});

new Vue({ el: "#app" });
```

```html
<div id="app">
  <component-a></component-a>
  <component-b></component-b>
  <component-c></component-c>
</div>
```

在所有子组件中也是如此，也就是说这三个组件在各自内部也都可以相互使用。
