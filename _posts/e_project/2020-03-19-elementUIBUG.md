---
layout: post
title: 组件库 ElementUI 使用问题汇总
summary:
categories: Project
technique: true
---


```javascript
// 日期选择选中不更新
<el-date-picker
  v-model="ruleForm.validityDate"
  type="datetimerange"
  start-placeholder="开始日期"
  end-placeholder="结束日期"
  :default-time="['00:00:00', '23:59:59']"
  style="width: 100%;"
  value-format="yyyy-MM-dd HH:mm:ss"
  @change="dateChange"
  @input="$forceUpdate()"
/>
// 下拉选择选中不更新
<el-select
  v-model="ruleForm.citiesBase"
  placeholder="请选择城市包"
  style="width: 100%;"
  value-key="id"
  @change="$forceUpdate()"
/>
// input 反显后无法删除修改
// v-model 使用data函数里双向数据绑定的变量，不要使用类似this.rules.a这样的变量
```
