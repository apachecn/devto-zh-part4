# 12 Console.xxx 你可能不知道

> 原文：<https://dev.to/0xkoji/12-console-xxx-you-may-not-know-429a>

我想我们很多人都经常使用 console.log，但是还有更多

## 控制台.表

```
const foo = { id: 1, verified: true, color: 'green' };
const bar = { id: 2, verified: false, color: 'red' };
console.table({ foo, bar}) 
```

Enter fullscreen mode Exit fullscreen mode

## 控制台

```
console.group('User Details');
console.log('name: John Doe');
console.log('job: Software Developer');
// Nested Group
console.group('Address');
console.log('Street: 123 Townsend Street');
console.log('City: San Francisco');
console.log('State: CA');
console.groupEnd();
console.groupEnd(); 
```

Enter fullscreen mode Exit fullscreen mode

## 控制台.警告

```
console.warn("warning") 
```

Enter fullscreen mode Exit fullscreen mode

## 控制台.错误

```
console.error("error") 
```

Enter fullscreen mode Exit fullscreen mode

## 控制台. info

```
console.info("info") 
```

Enter fullscreen mode Exit fullscreen mode

## 颜色标记

```
console.log('%c Auth ', 
            'color: white; background-color: #2274A5', 
            'Login page rendered');
console.log('%c GraphQL ', 
            'color: white; background-color: #95B46A', 
            'Get user details');
console.log('%c Error ', 
            'color: white; background-color: #D33F49', 
            'Error getting user details'); 
```

Enter fullscreen mode Exit fullscreen mode

## 控制台.时间

```
let i = 0;
console.time("While loop");
while (i < 1000000) {
  i++;
}
console.timeEnd("While loop");
console.time("For loop");
for (i = 0; i < 1000000; i++) {
  // For Loop
}
console.timeEnd("For loop"); 
```

Enter fullscreen mode Exit fullscreen mode

## 其他

### 控制台. trace

### 控制台.断言

### 控制台.调试

### 控制台.配置文件

### 控制台.目录