---
layout:     post
title:      Vritual DOM
summary: 虚拟DOM在DOM的基础上建立了一个抽象层，对数据和状态所做的任何改动，都会被自动且高效的同步到虚拟DOM，最后再批量同步到DOM中，可以最大的减少页面的重绘。
categories: Vue
technique: true
---

### 虚拟DOM

createElement( ): 用 JavaScript 对象(虚拟树) 描述 真实 DOM 对象(真实树)

diff(oldNode, newNode) : 对比新旧两个虚拟树的区别，收集差异

patch( ) : 将差异应用到真实 DOM 树

#### 浏览器引擎工作流程

![webkit引擎的处理流程](https://raw.githubusercontent.com/Selenamona/Selenamona.github.io/master/img/virtual-dom.jpg  'webkit引擎的处理流程')


1. `创建DOM tree`：用HTML分析器，分析HTML元素，构建DOM树。

2. `创建Style Rules`：用CSS分析器，分析CSS文件和元素上inline样式，生成页面样式表。

3. `构建Render tree`：关联DOM树和样式表，构建Render树，这一过程又称为Attachment。

    > 每个DOM节点都有attach方法，接受样式信息，返回一个render对象（又名renderer）。这些render对象最终会被构建成一颗Render树。

4. `布局Layout`：浏览器布局，为每个Render树上的节点确定一个在显示屏上出现的精确坐标值。

5. `绘制Painting`：调用每个节点的paint方法，让它们显示出来。


#### 虚拟DOM优势

当使用javascript直接操作DOM时，浏览器会从构建DOM树开始从头到尾执行一遍引擎工作流程。频繁操作DOM会出现页面卡顿，影响用户的体验。    

为解决这个浏览器性能问题，虚拟DOM在DOM的基础上建立了一个抽象层，对数据和状态所做的任何改动，都会被自动且高效的同步到虚拟DOM，最后再批量同步到DOM中，可以最大的减少页面的重绘。

``` html
<ul class="list">
    <li>item1</li>  
    <li>item2</li>  
</ul>
```
如上代码，若修改'item2'为'item3'，当数据改变，直接操作DOM会使整体重新渲染，而使用虚拟DOM就会局部刷新变化部分，只将'item2'这个文本节点变为'item3'。

************************************************************************

> Vritual DOM这个概念最先由React引入，是一种DOM对象差异化比较方案，即将DOM对象抽象成为Vritual DOM对象（即render()函数渲染的结果），然后通过差异算法对Vritual DOM进行对比并返回差异，最后通过一个补丁算法将返回的差异对象应用在真实DOM结点。

> Vue当中的Virtual DOM对象被称为VNode（template当中的内容会被编译为render()函数，而render()函数接收一个createElement()函数，并最终返回一个VNode对象），补丁算法来自于另外一个开源项目snabbdom，即将真实的DOM操作映射成对虚拟DOM的操作，通过减少对真实DOM的操作次数来提升性能。

> 虚拟DOM劣势：首次渲染大量DOM时因为多了一层虚拟DOM的计算，会比innerHTML插入方式慢，所以使用时尽量不要一次性渲染大量DOM。

#### DIFF算法

#### 实现简单虚拟DOM

**构建虚拟DOM：**

虚拟DOM，其实就是用JavaScript对象来构建DOM树，如上ul组件模版，其树形结构如下：

<img src="https://raw.githubusercontent.com/Selenamona/Selenamona.github.io/master/img/ul.png" width="300"/>

通过javascript构建：

```javascript
var elem = Element({
                tagName: 'ul',
                props: {'class': 'list'},
                children: [
                    Element({tagName: 'li', children: ['item1']}),
                    Element({tagName: 'li', children: ['item2']})
                ]
            });
```
> Element为一个构造函数，返回一个Element对象。为了更清晰的呈现虚拟DOM结构，省略了new，而在Element中实现。

```javascript
 
// * @Params:       
// *     tagName(string)(requered)                  
// *     props(object)(optional)                  
// *     children(array)(optional)                          
    
function Element({tagName, props, children}){
    //instanceof用于判断一个变量是否某个对象的实例
    if(!(this instanceof Element)){
        //返回一个Element对象
        return new Element({tagName, props, children})
    }
    this.tagName = tagName;
    this.props = props || {};
    this.children = children || [];
}
```

通过Element可以任意地构建虚拟DOM树。但虚拟终归是虚拟的，我们得**通过遍历，逐个节点地创建真实DOM节点**，将其呈现到页面中。

1. `createElement`   

2. `createTextNode`


> 树形结构遍历：      
    1. 深度优先遍历(DFS - Depth First Search) ：利用栈遍历数据         
        ![DFS](https://raw.githubusercontent.com/Selenamona/Selenamona.github.io/master/img/dfs.jpg)        
    2. 广度优先遍历(BFS - Breadth First Search)  ：利用队列遍历数据                     
        ![BFS](https://raw.githubusercontent.com/Selenamona/Selenamona.github.io/master/img/bfs.png)               


因为我们得将子节点append到父节点中，所以采用DFS：

```javascript
Element.prototype.render = function(){
    var el = document.createElement(this.tagName),
        props = this.props,
        propName,
        propValue;
    for(propName in props){
        propValue = props[propName];
        el.setAttribute(propName, propValue);
    }
    this.children.forEach(function(child){
        var childEl = null;
        if(child instanceof Element){
            childEl = child.render();
        }else{
            childEl = document.createTextNode(child);
        }
        el.appendChild(childEl);
    });
    return el;
};
```
将上ul虚拟DOM呈现到页面body中：

```html
var elem = Element({
                tagName: 'ul',
                props: {'class': 'list'},
                children: [
                    Element({tagName: 'li', children: ['item1']}),
                    Element({tagName: 'li', children: ['item2']})
                ]
            });
document.querySelector('body').appendChild(elem.render());
```

**处理DOM更新：**

DOM更新，无外乎四种情况，如下：

　　1. 新增节点;
　　2. 删除节点;
　　3. 替换节点;
　　4. 父节点相同，对比子节点.

因为要将变化的节点更新到真实DOM中，所以需传入真实的DOM根节点，并且真实的DOM节点与虚拟的DOM节点，树形结构一致，故通过标记可以记录节点变化位置，如下：

<img src="https://raw.githubusercontent.com/Selenamona/Selenamona.github.io/master/img/update-dom.png" width="500"/>

```javascript
function updateElement($root, newElem, oldElem, index = 0) {
    if (!oldElem){
        $root.appendChild(newElem.render());
    } else if (!newElem) {
        $root.removeChild($root.childNodes[index]);
    } else if (changed(newElem, oldElem)) {
        if (typeof newElem === 'string') {
            $root.childNodes[index].textContent = newElem;
        } else {
            $root.replaceChild(newElem.render(), $root.childNodes[index]);
        }
    } else if (newElem.tagName) {
        let newLen = newElem.children.length;
        let oldLen = oldElem.children.length;
        for (let i = 0; i < newLen || i < oldLen; i++) {
            updateElement($root.childNodes[index], newElem.children[i], oldElem.children[i], i)
        }
    }
}

function changed(elem1, elem2) {
    return (typeof elem1 !== typeof elem2) ||
           (typeof elem1 === 'string' && elem1 !== elem2) ||
           (elem1.type !== elem2.type);
}
``` 

> 参考链接：   
    [实现一个简单的虚拟DOM / 猴子](https://www.cnblogs.com/giggle/p/7538533.html)     
    [VirtualDOM与diff(Vue实现) / 染陌](https://blog.csdn.net/github_36546211/article/details/78023747)     
    [深入Vue2.x的虚拟DOM diff原理 / 小时光茶社](https://blog.csdn.net/m6i37jk/article/details/78140159)      


**效果演示：**

```html
<button id="refresh">refresh element</button>
<div id="root"></div>
```

```javascript
var newElem =  Element({
  tagName: 'ul',
  props: {'class': 'list'},
  children: [
      Element({tagName: 'li', children: ['item1']}),
      Element({tagName: 'li', children: ['hahaha']})
  ]
});
var $root = document.querySelector('#root');
var $refresh = document.querySelector('#refresh');
updateElement($root, elem);
$refresh.addEventListener('click', () => {
  updateElement($root, newElem, elem);
});
```


**************************

[深入框架本源系列 —— Virtual Dom](https://juejin.im/post/5b10dd36e51d4506e04cf802)







