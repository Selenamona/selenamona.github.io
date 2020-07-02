---
layout:     post
title:     数字相加的精读问题 -number
summary:  
categories: Js
technique: true
---

 
`0.1 + 0.2 // 0.30000000000000004`

将 0.1 和 0.2 换成二进制表示：
    0.1 -> 0.0001100110011001...(无限)
    0.2 -> 0.0011001100110011...(无限)

因为 IEEE 754 标准的 64 位双精度浮点数的小数部分最多支持 53 位二进制位，所以两者相加之后得到二进制为：
`0.0100110011001100110011001100110011001100110011001100`

因为浮点数小数位的限制，这个二进制数字被截断了，用这个二进制数转换成十进制，就成了 `0.30000000000000004`，从而在进行算数计算时产生误差。

JavaScript 中 Number 类型统一按浮点数处理，整数也如此：

`console.log(19571992547450991); // 19571992547450990`
`console.log(19571992547450991 === 19571992547450994); // true`

整数的安全范围是： [-9007199254740991, 9007199254740991]，超过这个范围的，就存在被舍去的精度问题。这个问题并不仅仅存在于 JavaScript 中，几乎所有采用了 IEEE-745 标准的编程语言，都会有这个问题，只不过在很多其他语言中已经封装好了方法来避免精度的问题。而因为 JavaScript 是一门弱类型的语言，从设计思想上就没有对浮点数有个严格的数据类型，所以精度误差的问题就显得格外突出。

```javascript
// 最大值
const MaxNumber = Math.pow(2, 53) - 1;
console.log(MaxNumber); // 9007199254740991
console.log(Number.MAX_SAFE_INTEGER); // 9007199254740991

// 最小值
const MinNumber = -(Math.pow(2, 53) - 1);
console.log(MinNumber); // -9007199254740991
console.log(Number.MIN_SAFE_INTEGER); // -9007199254740991 
```

## | 解决办法

**numObj.toFixed(digits)**
 
- digits：小数点后数字的个数；介于 0 到 20 （包括）之间，实现环境可能支持更大范围。如果忽略该参数，则默认为 0
- toFixed() 得出的结果是 String 类型

```javascript
console.log(1.0 - 0.9); // 0.09999999999999998
console.log(0.3 / 0.1); // 2.9999999999999996
console.log(9.7 * 100); // 969.9999999999999
console.log(2.22 + 0.1); // 2.3200000000000003
// 使用 toFixed()： 
parseFloat((1.0 - 0.9).toFixed(10)); // 0.1   
parseFloat((0.3 / 0.1).toFixed(10)); // 3  
parseFloat((9.7 * 100).toFixed(10)); // 970
parseFloat((2.22 + 0.1).toFixed(10)); // 2.32 

parseFloat(1.005.toFixed(2)) // 1
```

结果证明：toFixed() 也不是最保险的解决方式。


***自定义方法***

```javascript
/**
 * @name 检测数据是否超限
 * @param {Number} number 
 */
const checkSafeNumber = (number) => {
  if (number > Number.MAX_SAFE_INTEGER || number < Number.MIN_SAFE_INTEGER) {
    console.log(`数字 ${number} 超限，请注意风险！`);
  }
};

/**
 * @name 修正数据
 * @param {Number} number 需要修正的数字
 * @param {Number} precision 端正的位数
 */
const revise = (number, precision = 12) => {
  return +parseFloat(number.toPrecision(precision));
}

/**
 * @name 获取小数点后面的长度
 * @param {Number} 需要转换的数字
 */
const digitLength = (number) => {
  return (number.toString().split('.')[1] || '').length;
};

/**
 * @name 将数字的小数点去掉
 * @param {Number} 需要转换的数字
 */
const floatToInt = (number) => {
  return Number(number.toString().replace('.', ''));
};

/**
 * @name 精度计算乘法
 * @param {Number} arg1 乘数 1
 * @param {Number} arg2 乘数 2
 */
const multiplication = (arg1, arg2) => {
  const baseNum = digitLength(arg1) + digitLength(arg2);
  const result = floatToInt(arg1) * floatToInt(arg2);
  checkSafeNumber(result);
  return result / Math.pow(10, baseNum); 
};

console.log('------\n乘法：');
console.log(9.7 * 100); // 969.9999999999999
console.log(multiplication(9.7, 100)); // 970

console.log(0.01 * 0.07); // 0.0007000000000000001
console.log(multiplication(0.01, 0.07)); // 0.0007

console.log(1207.41 * 100); // 120741.00000000001
console.log(multiplication(1207.41, 100)); // 0.0007

/**
 * @name 精度计算加法
 * @description JavaScript 的加法结果存在误差，两个浮点数 0.1 + 0.2 !== 0.3，使用这方法能去除误差。
 * @param {Number} arg1 加数 1
 * @param {Number} arg2 加数 2
 * @return arg1 + arg2
 */
const add = (arg1, arg2) => {
  const baseNum = Math.pow(10, Math.max(digitLength(arg1), digitLength(arg2)));
  return (multiplication(arg1, baseNum) + multiplication(arg2, baseNum)) / baseNum;
}

console.log('------\n加法：');
console.log(1.001 + 0.003); // 1.0039999999999998
console.log(add(1.001, 0.003)); // 1.004

console.log(3.001 + 0.07); // 3.0709999999999997
console.log(add(3.001, 0.07)); // 3.071

/**
 * @name 精度计算减法
 * @param {Number} arg1 减数 1
 * @param {Number} arg2 减数 2
 */
const subtraction = (arg1, arg2) => {
  const baseNum = Math.pow(10, Math.max(digitLength(arg1), digitLength(arg2)));
  return (multiplication(arg1, baseNum) - multiplication(arg2, baseNum)) / baseNum;
};

console.log('------\n减法：');
console.log(0.3 - 0.1); // 0.19999999999999998
console.log(subtraction(0.3, 0.1)); // 0.2

/**
 * @name 精度计算除法
 * @param {Number} arg1 除数 1
 * @param {Number} arg2 除数 2
 */
const division = (arg1, arg2) => {
  const baseNum = Math.pow(10, Math.max(digitLength(arg1), digitLength(arg2)));
  return multiplication(arg1, baseNum) / multiplication(arg2, baseNum);
};

console.log('------\n除法：');
console.log(0.3 / 0.1); // 2.9999999999999996
console.log(division(0.3, 0.1)); // 3

console.log(1.21 / 1.1); // 1.0999999999999999
console.log(division(1.21, 1.1)); // 1.1

console.log(1.02 / 1.1); // 0.9272727272727272
console.log(division(1.02, 1.1)); // 数字 9272727272727272 超限，请注意风险！0.9272727272727272

console.log(1207.41 / 100); // 12.074100000000001
console.log(division(1207.41, 100)); // 12.0741

/**
 * @name 按指定位数四舍五入
 * @param {Number} number 需要取舍的数字
 * @param {Number} ratio 精确到多少位小数
 */
const round = (number, ratio) => {
  const baseNum = Math.pow(10, ratio);
  return division(Math.round(multiplication(number, baseNum)), baseNum);
  // Math.round() 进行小数点后一位四舍五入是否有问题，如果有，请证明出来
  // https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Math/round
}

console.log('------\n四舍五入：');
console.log(0.105.toFixed(2)); // '0.10'
console.log(round(0.105, 2)); // 0.11

console.log(1.335.toFixed(2)); // '1.33'
console.log(round(1.335, 2)); // 1.34

console.log(-round(2.5, 0)); // -3
console.log(-round(20.51, 0)); // -21
 
```

***框架***


[Math.js](https://mathjs.org/) 是一个用于 JavaScript 和 Node.js 的扩展数学库。

[decimal.js](http://mikemcl.github.io/decimal.js/) JavaScript 的任意精度的十进制类型。

[big.js](http://mikemcl.github.io/big.js/) 一个小型，快速，易于使用的库，用于任意精度的十进制算术运算。


[bignumber.js](https://mikemcl.github.io/bignumber.js/) 一个用于任意精度算术的 JavaScript 库。




