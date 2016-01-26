# 转载翻译https://github.com/loverajoel/jstips 

## #0 - 向数组中插入项
> 2015-12-29

向一个已经存在的数组中插入一项是非常常见的.你可以用`push`向数组的末尾添加,使用`unshift`向头部添加,或者用`splice`向中间插入.

这些都是已知的方法,但是并不意味没有性能更好的方法,开始咯:

使用`push()`向数组的尾部添加一项是非常容易的,但是有一种性能更高的方法

```javascript
var arr = [1,2,3,4,5];

arr.push(6);
arr[arr.length] = 6; // 43% faster in Chrome 47.0.2526.106 on Mac OS X 10.11.1
```
以上方法都修改了原始的数组,不相信我么?请查看[jsperf](http://jsperf.com/push-item-inside-an-array)

现在如果我们尝试在数组头部添加一项

```javascript
var arr = [1,2,3,4,5];

arr.unshift(0);
[0].concat(arr); // 98% faster in Chrome 47.0.2526.106 on Mac OS X 10.11.1
```
现在有一点细节: `unshift`修改了原来的数组;`concat`返回了一个新的数组.[jsperf](http://jsperf.com/unshift-item-inside-an-array)

使用`splice`向数组中添加项是很容易的,并且是性能最高的方式.

```javascript
var items = ['one', 'two', 'three', 'four'];
items.splice(items.length / 2, 0, 'hello');
```

我尝试运行了这些测试在一系列的浏览器和操作系统中并且结果都很相近,我希望这些tips对你有用并且鼓励你去自己测试

## #1 - AngularJs: `$digest` 对比 `$apply`

> 2016-01-01  by [@loverajoel](https://twitter.com/loverajoel)

AngularJs最为人称道的一个特点就是双向数据绑定,AngularJs通过周期性的`$digest`来对比model和view之间的变化来实现它.想要知道框架的作用原理,就需要理解这个概念.

当事件触发时,Angular会比较每个watcher.这就是所谓的`$digest`循环.
