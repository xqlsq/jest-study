# mock（模拟）

## mock 函数

mock函数可以通过擦除实际的函数实现，很简单的测试代码之间的联系。捕获函数的调用（包括这些调用传入的参数），捕获构造函数的实例。并可以配置返回值。

## 如何使用

打个比方，数据的forEach方法传入一个回调函数，这个回调函数的参数分别是：value、index、arr
```
var fn = jest.fn();

var arr = [1, 2, 3];

test("Array forEach callback test", () => {
    arr.forEach((value, index, arr) => {
        console.log(value, index, arr);
    });
    /**
     * 打印结果
     * 1    0   [ 1, 2, 3 ]
     * 2    1   [ 1, 2, 3 ]
     * 3    2   [ 1, 2, 3 ]
     */
    arr.forEach(fn);
    expect(fn.mock.calls.length).toBe(3);
    expect(fn.mock.calls[0][0]).toBe(1);
    expect(fn.mock.calls[1][1]).toBe(1);
    expect(fn.mock.calls[2][2]).toEqual(arr);
});
```

## .mock属性

所有的mock函数都有一个特别的属性：`.mock`,保存了如何调用函数的数据。还可以追踪查看实例
```
const myMock = jest.fn();

const a = new myMock();
const b = {
    name: 'xubo'
};
const bound = myMock.bind(b);
bound();
it();
console.log(myMock.mock.instances);
// 打印：
// console.log test.test.js:10
// [ mockConstructor {}, { name: 'xubo' } ]
```

## mock 函数返回值

在测试用例里面。mock函数也会用来注入测试值。
```
const myMock = jest.fn();
console.log(myMock());
// > undefined

myMock
  .mockReturnValueOnce(10)
  .mockReturnValueOnce('x')
  .mockReturnValue(true);

console.log(myMock(), myMock(), myMock(), myMock());
// > 10, 'x', true, true
```

## 定时器Mock

原生的定时器对于测试环境来说不是很完美。因为它们是根据真实的时间计算的。Jest可以用能让你控制时间流逝的功能来替换原生的计时器。

你可以使用`jest.useFakeTimers()`来mock定时器功能：
```
// timerGame.js
'use strict';

function timerGame(callback) {
  console.log('Ready....go!');
  setTimeout(() => {
    console.log('Times up -- stop!');
    callback && callback();
  }, 1000);
}

module.exports = timerGame;
```
```
// __tests__/timerGame-test.js
'use strict';

jest.useFakeTimers();

test('waits 1 second before ending the game', () => {
  const timerGame = require('../timerGame');
  timerGame();

  expect(setTimeout).toHaveBeenCalledTimes(1);
  expect(setTimeout).toHaveBeenLastCalledWith(expect.any(Function), 1000);
});
```

### 运行所有的定时器

另外我们可能想要运行这样一个测试用例：定时器回调函数在1s后被调用了。我们可以用Jest的定时器的api把定时器的时间提前使回调函数提前运行。
```
test('calls the callback after 1 second', () => {
  const timerGame = require('../timerGame');
  const callback = jest.fn();

  timerGame(callback);

  // At this point in time, the callback should not have been called yet
  expect(callback).not.toBeCalled();

  // Fast-forward until all timers have been executed
  jest.runAllTimers();

  // Now our callback should have been called!
  expect(callback).toBeCalled();
  expect(callback).toHaveBeenCalledTimes(1);
});
```

### 运行等待定时器

也有一些场景，您可能有一个递归计时器——它在自己的回调中设置一个新的计时器。如果运行所有的定时器，会陷入死循环，所以像` jest.runAllTimers()`是不能使用的，`jest.runOnlyPendingTimers()`就可以派上用场了：
```
// infiniteTimerGame.js
'use strict';

function infiniteTimerGame(callback) {
  console.log('Ready....go!');

  setTimeout(() => {
    console.log('Times up! 10 seconds before the next game starts...');
    callback && callback();

    // Schedule the next game in 10 seconds
    setTimeout(() => {
      infiniteTimerGame(callback);
    }, 10000);
  }, 1000);
}

module.exports = infiniteTimerGame;
```
```
// __tests__/infiniteTimerGame-test.js
'use strict';

jest.useFakeTimers();

describe('infiniteTimerGame', () => {
  test('schedules a 10-second timer after 1 second', () => {
    const infiniteTimerGame = require('../infiniteTimerGame');
    const callback = jest.fn();

    infiniteTimerGame(callback);

    // At this point in time, there should have been a single call to
    // setTimeout to schedule the end of the game in 1 second.
    expect(setTimeout).toHaveBeenCalledTimes(1);
    expect(setTimeout).toHaveBeenLastCalledWith(expect.any(Function), 1000);

    // Fast forward and exhaust only currently pending timers
    // (but not any new timers that get created during that process)
    jest.runOnlyPendingTimers();

    // At this point, our 1-second timer should have fired it's callback
    expect(callback).toBeCalled();

    // And it should have created a new timer to start the game over in
    // 10 seconds
    expect(setTimeout).toHaveBeenCalledTimes(2);
    expect(setTimeout).toHaveBeenLastCalledWith(expect.any(Function), 10000);
  });
});
```

### 定时器提前执行

`runTimersToTime`（Jest22.0.0: `advanceTimersByTime`）

也可以使用`jest.advanceTimersByTime(msToRun)`，当这个api被调用的时候，所有的定时器都会被提前`msToRun`毫秒被执行，所有被`setTimeout`和`setInterval`推入主任务执行队列的代码将会被执行。另外在`msToRun`时间内执行的主任务也会执行。直到队列中不再有任何应该在msToRun毫秒内运行的宏任务。
```
// timerGame.js
'use strict';

function timerGame(callback) {
  console.log('Ready....go!');
  setTimeout(() => {
    console.log('Times up -- stop!');
    callback && callback();
  }, 1000);
}

module.exports = timerGame;
it('calls the callback after 1 second via advanceTimersByTime', () => {
  const timerGame = require('../timerGame');
  const callback = jest.fn();

  timerGame(callback);

  // At this point in time, the callback should not have been called yet
  expect(callback).not.toBeCalled();

  // Fast-forward until all timers have been executed
  jest.advanceTimersByTime(1000);

  // Now our callback should have been called!
  expect(callback).toBeCalled();
  expect(callback).toHaveBeenCalledTimes(1);
});
```
最后，在某些测试中偶尔可能会清除所有待定定时器。为此，我们可以使用`jest.clearAllTimers()`。
这个例子的代码可以在 [examples / timer](../examples/timer)中找到。




var mock_name = 'xubo';
部分mock module： jest.mock(moduleName, () => {
  var trueModule = require.requireActual(moduleName); // 引入真实模块的输出
  var mockModule = jest.genMockFromModule(moduleName); // 引入mock模块的输出
  return { // return 是mock该模块的最终结果.需要注意的是，如果该对象的值为引用的变量，该变量名需要加上前缀 mock.
    default: default....... // default代表es6的默认输出export.default
    name: mock_name,
    ...others // 其他export的输出
  }
})

如下所示
```
.
├── config
├── __mocks__
│   └── fs.js
├── models
│   ├── __mocks__
│   │   └── user.js
│   └── user.js
├── node_modules
└── views
```
人为模拟通常被用来