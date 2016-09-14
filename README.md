# fis3-parser-vuecomponent

基于fis3-parser-vue-component修改


## 安装配置

安装：`npm install fis3-parser-vuecomponent --save-dev`。


配置：
```javascript:;
// vue组件本身配置
fis.match('src/vue/**.vue', {
  isMod: true,
  rExt: 'js',
  useSameNameRequire: true,
  parser: fis.plugin('vue-component', {
    cssScopeFlag: 'vuec'
  })
});

// vue组件中产出的css处理。
fis.match('src/(vue/**.css)', {
  release: 'css/$1'
});

// vue组件中的less片段处理
fis.match('src/vue/**.vue:less', {
  rExt: 'css',
  parser: fis.plugin('less'),
  release: 'xxx' // 这个无效
});

// 注意：因为组件中的样式片段编译只是编译内容，所以上面的release配置是无效的。要配置其release，需要针对生成的css文件：
fis.match('src/vue/(**.css)', {
  release: '/vue-style/$1'
});

// vue组件中的sass片段处理
fis.match('src/vue/**.vue:scss', {
  rExt: 'css',
  parser: fis.plugin('node-sass')
});

// vue组件中的jade模板片段处理
fis.match('src/**.vue:jade', {
  rExt: 'css',
  parser: fis.plugin('less')
});

// vue组件中js片段处理。
fis.match('src/**.vue:js', {
  parser: [
    fis.plugin('babel-6.x', {
      presets: ['es2015-loose', 'react', 'stage-3']
    }),
    fis.plugin('translate-es3ify', null, 'append')
  ]
})

// vue组件中的产出coffee片段处理
fis.match('src/**.vue:coffee', {
  rExt: 'html',
  parser: fis.plugin('jade')
})
```

## css scoped支持

> 为了保证每一个组件样式的独立性，是当前组件定义的样式只在当前的组件内生效，引入css scoped机制。

1. 在模板的元素上（一般是根节点）加上scoped标志(class,attr,id)，默认为`__vuec__`， 你可以通过`cssScopedFlag`自定义。可以加在class，或者属性，或者id。
```html
<template>
  <div class="test" __vuec__></div>
  <div class="test __vuec__"></div>
</template>
```
2. 在样式中使用scoped标志。
```css
.test[__vuec__] {
  //
}
// or
.other.__vuec__ {

}
// or
.__vuec__ {
  .a {

  }
}
```
3. scoped标志会根据文件路径生成唯一的hash字符串（如：`_v-23j232jj` ）;

4. 配置：scoped标志默认为'__vuec__'，你可以自定义。
```js
fis.match('src/**.vue', {
  isMod: true,
  rExt: 'js',
  useSameNameRequire: true,
  parser: fis.plugin('vue-component', {
    ccssScopedFlag: '__vuec__', // 组件scoped占位符
    cssScopedIdPrefix: '_v-',   // hash前缀：_v-23j232jj
    cssScopedHashType: 'sum',   // hash生成模式，num：使用`hash-sum`, md5: 使用`fis.util.md5`
    cssScopedHashLength: 8,     // hash 长度，cssScopedHashType为md5时有效
    styleNameJoin: ''           // 样式文件命名连接符 `component-a.css`
  })
});
```