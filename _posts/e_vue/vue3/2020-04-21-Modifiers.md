---
layout:     post
title:      修饰符
summary:
categories: Vue3
technique: true
---


```html
<!-- the click event's propagation will be stopped -->
<a @click.stop="doThis"></a>

<!-- the submit event will no longer reload the page -->
<form @submit.prevent="onSubmit"></form>

<!-- modifiers can be chained -->
<!-- @click.prevent.self will prevent all clicks while -->
<!-- @click.self.prevent will only prevent clicks on the element itself. -->
<a @click.stop.prevent="doThat"></a>

<!-- just the modifier -->
<form @submit.prevent></form>

<!-- use capture mode when adding the event listener -->
<!-- i.e. an event targeting an inner element is handled here before being handled by that element -->
<div @click.capture="doThis">...</div>

<!-- only trigger handler if event.target is the element itself -->
<!-- i.e. not from a child element -->
<div @click.self="doThat">...</div>

<!-- the scroll event's default behavior (scrolling) will happen -->
<!-- immediately, instead of waiting for `onScroll` to complete  -->
<!-- in case it contains `event.preventDefault()`                -->
<div @scroll.passive="onScroll">...</div>

<!-- only call `vm.submit()` when the `key` is `Enter` -->
<input @keyup.enter="submit" />

<!-- Alt + Enter -->
<input @keyup.alt.enter="clear" />

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>

<input v-model.lazy="msg" />
<input v-model.number="age" type="number" />
<input v-model.trim="msg" />
```

### Event Modifiers

.stop
.prevent
.capture
.self
.once
.passive

### Key Modifiers

.enter
.tab
.delete (captures both "Delete" and "Backspace" keys)
.esc
.space
.up
.down
.left
.right

### System Modifier Keys
.ctrl
.alt
.shift
.meta

### .exact Modifier
.exact

### Mouse Button Modifiers

.left
.right
.middle

### input Modifiers
.lazy
.number
.trim
