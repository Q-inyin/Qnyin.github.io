---
title: JavaScript中数组遍历的方式
date: 2022-11-17 02:19:07
updated: 2026-04-21 02:19:07
categories: ['JavaScript']
tags: ['js','数组','分享']
cover: https://w.wallhaven.cc/full/z8/wallhaven-z8kkxo.jpg
---

#### 整理了一些数组遍历的方式

### 1. for循环

```javascript
//最简单的一种循环遍历方式,也是使用频率最高的
let arr = [1,2,3,4,5]
for(let i = 0;i<arr.length;i++){
    //打印每一个数组的元素
    console.log(arr[i])
}
```

### 2.for in（对象遍历）

```javascript
for(let key in obj){
    console.log(arr[key]) //输出的key是数组的索引值
}
```

### 3. for of （不能循环对象）

```javascript
for(let val of arr){
    console.log(val)
}
```

### 4.ES6 中的 forEach( ) 数组元素有几个，该方法的回调就会执行几次

```javascript
arr.forEach((item,index,arr)=>{
    console.log(item + '---' + index + '---' + arr)
})
```

~~~javascript
const array1 = ['a', 'b', 'c'];

array1.forEach(element => console.log(element));
~~~

### 5.map映射一个数组或者对象,也可以遍历json对象

~~~javascript
const array1 = [1, 4, 9, 16];

// pass a function to map
const map1 = array1.map(x => x * 2);

console.log(map1);
// expected output: Array [2, 8, 18, 32]
~~~

### 6.filter 过滤遍历数组，过滤出符合条件的元素并返回一个新数组

```javascript
const words = ['spray', 'limit', 'elite','exuberant', 'destruction', 'present'];

const result = words.filter(word => word.length > 6);

console.log(result);
// expected output: Array ["exuberant","destruction", "present"]
```

### 7.some 遍历数组，只要有一个以上的元素满足条件 就返回true ，否则返回 false

```javascript
const array = [1, 2, 3, 4, 5];

// checks whether an element is even
const even = (element) => element % 2 === 0;

console.log(array.some(even));
// expected output: true
```

### 8.every 遍历数组，每一个元素都满足条件 则返回 true 否则返回 false

```javascript
let somebool = datalsit.some((item,index)=>{
    return item.done
})
// 返回的是布尔值  
```

### 9.find ES6 遍历数组，返回符合条件的第一个元素，如果没有符合条件的元素则返回 undefined

```javascript
let arr1 = [1,2,3,4,5,6,7]
let num = arr1.find((item,index)=>{
    return item === 3 
})
console.log(num) // 3 
```

### 10.findIndex ES6遍历数组，返回符合条件的一个元素的索引，如果没有符合条件的元素则返回-1

```javascript
let arr2 = [1,2,3,5,7,8,2,4,5];
let num = arr2.findIndex((item,index)=>{
      return item === 3 
})
console.log(num) //2
```