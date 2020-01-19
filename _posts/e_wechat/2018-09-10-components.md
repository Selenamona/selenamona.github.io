---
layout:     post
title:      小程序组件通讯
summary:
categories: Applet
technique: true
---


## | 组件引用配置文件 JSON

自定义组件 json 中设置： `"component": true`

引用自定义组件的页面 json 设置：

```javascript
{
    "usingComponents": {
        "component-tag-name": "../component/component"
    }
}
```

#### WXML文件

在组件模板中可以提供一个 <slot> 节点，用于承载组件引用时提供的子节点。需要使用多slot时，可以在组件js中声明启用 options: {multipleSlots: true }，以不同的 name 来区分。

```html
<!-- 自定义组件的内部 WXML 结构(component.wxml)-->
<view class='wapper'>
  <text>this is component</text>
  <slot name="slot1"></slot>
  我在中间
  <slot name="slot2"></slot>
</view>

<!-- 以下是对一个自定义组件的引用 (page.wxml)-->
<view>
  <text>This is Page</text>
  <component-tag-name inner-text="Some text" class="page-component">
    <view slot="slot1" class="slot">来自page页面，通过slot标签</view>
    <view slot="slot2"></view>
  </component-tag-name>
</view>
```

```javascript
// 自定义组件 js (component.js)
Component({
  options: {
    multipleSlots: true // 在组件定义时的选项中启用多slot支持
  },
  properties: {
    // 这里定义了innerText属性，属性值可以在组件使用时指定
    innerText: {
      type: String,
      value: 'default value', //不存在此属性时
    }
  },
  data: {
    // 这里是一些组件内部数据
    someData: {}
  },
  methods: {
    // 这里是一个自定义方法
    customMethod: function(){}
  }
})
```

注：除继承样式（font/color等）外，app.wxss 中的样式、组件所在页面的的样式对自定义组件无效。

## | Page 与 Component 数据交互

**page > component**
- page在引用组件时能通过属性值设置，component.js在properties获取。
- page在引用组件时通data-xxx设置，component.js在this.dataset获取。

```javascript
// page.wxml
<component-tag-name fromPage="来自Page" data-other="from dataset"></component-tag-name>
// component.js
Component({
  properties: {
    formPage: String  //简写
    /*
    myProperty: { // 属性名
      type: String, // 类型（必填），
      // 目前接受的类型包括：String, Number, Boolean, Object, Array, null（表示任意类型）
      value: '', // 属性初始值（可选），如果未指定则会根据类型选择一个
      observer: function(newVal, oldVal){} // 属性被改变时执行的函数（可选），
      // 也可以写成在methods段中定义的方法名字符串, 如：'_propertyChange'
    }
    */
  },
  attached:function(){
    console.log(this.properties.fromPage);
    console.log(this.data.fromPage); //用data也能访问properties
    //设置properties值用setData()
    this.setData({
      fromPage: '改变了'
    });
    console.log(this.properties.fromPage);
    //通过dataset获取data-other的值
    console.log(this.dataset.other);
  }
})
```

👉 **父组件调用子组件方法**

```html
<!-- 父页面 -->
<button bindtap="uploadImg">上传图片</button>
<!-- 子组件 upload-img -->
<upload-img id="upload-img"></upload-img>
```
```javascript
  uploadImg() {
    // 调用子组件的方法 choosePhoto()
    this.selectComponent("#upload-img").choosePhoto();
  },
```

**component > page**

组件中的变量要传递到 page 页面可以通过事件触发 this.triggerEvent() 来实行

```javascript
// page.wxml
// 当自定义组件触发“myevent”事件时，调用“onMyEvent”方法
<component-tag-name bindmyevent="onMyEvent" />

//page.js
Page({
  onMyEvent: function(e){
    console.log(e.detail.msg);
  }
})

// component.wxml
// 在自定义组件中
<button bindtap="onTap">点击这个按钮将触发“myevent”事件</button>

//component.js
Component({
  methods: {
    onTap: function () {
      var myEventDetail = {msg:'来自component的信息'} // detail对象，提供给事件监听函数
      var myEventOption = {} // 触发事件的选项
      this.triggerEvent('myevent', myEventDetail, myEventOption)
    }
  }
})
```

## | behaviors

behaviors 是用于组件间代码共享的特性。每个 behavior 可以包含一组属性、数据、生命周期函数和方法，组件引用它时，它的属性、数据和方法会被合并到组件中，生命周期函数也会在对应时机被调用。每个组件可以引用多个 behavior 。 behavior 也可以引用其他 behavior 。
behavior 需要使用 Behavior() 构造器定义。

```javascript
// my-behavior.js
module.exports = Behavior({
  behaviors: [],
  properties: {
    myBehaviorProperty: {
      type: String
    }
  },
  data: {
    myBehaviorData: {}
  },
  attached: function(){},
  methods: {
    myBehaviorMethod: function(){}
  }
})
// 组件引用时，在behaviors定义段中将它们逐个列出即可。

// my-component.js
var myBehavior = require('my-behavior')
Component({
  behaviors: [myBehavior],
  properties: {
    myProperty: {
      type: String
    }
  },
  data: {
    myData: {}
  },
  attached: function(){},
  methods: {
    myMethod: function(){}
  }
})
```

在上例中，my-component 中最终包含 myBehaviorProperty、myProperty 两个属性，myBehaviorData、myData 两个数据字段，和myBehaviorMethod、myMethod 两个方法。当组件触发 attached 生命周期时，会依次触发 my-behavior 中的 attached 生命周期函数和my-component 中的 attached 生命周期函数。

组件和它引用的 behavior 中可以包含同名的字段，对这些字段的处理方法如下：

- 如果有同名的属性或方法，组件本身的属性或方法会覆盖 behavior 中的属性或方法，如果引用了多个 behavior ，在定义段中靠后 behavior 中的属性或方法会覆盖靠前的属性或方法；
- 如果有同名的数据字段，如果数据是对象类型，会进行对象合并，如果是非对象类型则会进行相互覆盖；
- 生命周期函数不会相互覆盖，而是在对应触发时机被逐个调用。如果同一个 behavior 被一个组件多次引用，它定义的生命周期函数只会被执行一次。

## | 组件间关系

两个自定义组件间的嵌套关系（parent父 / child 子/ ancestor祖先 / descendant子孙），通过relations 定义段来相互间的通信（必须在两个组件定义中都加入 relations 定义，否则不会生效）。

```html
<!-- page.wxml -->
<custom-ul>
  <custom-li> item 1 </custom-li>
  <custom-li> item 2 </custom-li>
</custom-ul>

<!-- component.wxml -->
<view class='wapper'>
  <text>custom-ul</text>
  <slot></slot>
</view>


<!-- children-component.wxml -->
<view>
  <text>li-item</text>
  <slot></slot>
</view>
```

```javascript
//page.json
{
  "usingComponents": {
    "custom-ul": "../component/component",
    "custom-li": "../component/children-component"
  }
}

//component.json
{
  "component": true
}

//children-component.json
{
  "component": true
}
//component.js
Component({
  relations:{
    'children-component': {  //子组件相对路径
      type: 'child', // 关联的目标节点应为子节点
      linked: function (target) {
        // 每次有custom-li被插入时执行，target是该节点实例对象，触发在该节点attached生命周期之后
        console.log('--linked--');
        console.log(target);
      },
      linkChanged: function (target) {
        // 每次有custom-li被移动后执行，target是该节点实例对象，触发在该节点moved生命周期之后
      },
      unlinked: function (target) {
        // 每次有custom-li被移除时执行，target是该节点实例对象，触发在该节点detached生命周期之后
      }
    }
  },
  methods: {
    _getAllLi: function () {
      // 使用getRelationNodes可以获得nodes数组，包含所有已关联的custom-li，且是有序的
      var nodes = this.getRelationNodes('children-component');
      console.log('--getAllLi--');
      console.log(nodes);
    }
  },
  ready: function () {
    this._getAllLi()
  }
})

//children-component.js
Component({
  data:{
    name: 'name'
  },
  relations: {
    'component': {  //父组件相对路径
      type: 'parent', // 关联的目标节点应为父节点
      linked: function (target) {
        // 每次被插入到custom-ul时执行，target是custom-ul节点实例对象，触发在attached生命周期之后
      },
      linkChanged: function (target) {
        // 每次被移动后执行，target是custom-ul节点实例对象，触发在moved生命周期之后
      },
      unlinked: function (target) {
        // 每次被移除时执行，target是custom-ul节点实例对象，触发在detached生命周期之后
      }
    }
  }
})
```
