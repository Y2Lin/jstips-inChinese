## #0 - 向数组中插入项
> 2015-12-29

向一个已经存在的数组中插入一项是非常常见的.你可以用`push`向数组的末尾添加,使用`unshift`向头部添加,或者用`splice`向中间插入.

这些都是已知的方法,但是并不意味着这是一个高性能的方法,开始咯:

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
