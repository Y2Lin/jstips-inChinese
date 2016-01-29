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

我尝试在一系列的浏览器和操作系统中运行了这些测试并且结果都很相近.我希望这些tips对你有用并且鼓励你去自己测试.

## #1 - AngularJs: `$digest` 对比 `$apply`

> 2016-01-01  by [@loverajoel](https://twitter.com/loverajoel)

AngularJs最为人称道的一个特点就是双向数据绑定,AngularJs通过周期性的`$digest`来检查model和view之间的变化来实现它.想要知道框架的作用原理,就需要理解这个概念.

当事件触发时,Angular会检查每个watcher.这就是所谓的`$digest`循环.有时候你不得不手动的触发一次新的检查循环,由于这个操作是最影响性能的操作之一,所以你必须做出一个正确的选择.

### `$apply`

这是显然是一个让你开始检测循环的方法.这意味着所有的**watcher**都被检查;整个程序开始进行`$diges loop`.这意味着这些**watcher**都被检查了.在内部,执行一个可选的回调函数后,调用了`$rootScope.$digest()`;

### `$digest`

在这个例子中`$digest`方法开始对当前scope以及其子scope进行`$digest`遍历.需要注意的是,父级scope不会被检查和影响.

### 建议
- 在AngularJS以外,只有当DOM事件触发时才使用`$apply`或者`$digest`.
- 传递一个函数表达式给 `$apply`,有一个容错机制,可以一次检测循环中整合多个变化.

  ```javascript
    $scope.$apply(() => {
      $scope.tip = 'Javascript Tip';
    });
  ```

- 如果你只需要更新当前的scope和它的子scope,使用`$digest`,并且阻止对整个程序的检测循环.对性能的提升是不言而喻的.

- `$apply`是十分耗费机器资源的,尤其是当页面有较多的数据绑定时.
- 如果你使用的是AngularJS 1.2x以上版本,使用`$evalAsync`,这是一个核心的方法,用来在本次或者下次检测循环中转化表达式.这会提高你的程序性能.