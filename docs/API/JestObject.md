# Jest对象

## jest.resetModules();

注册模块重置，清除引入模块所带来的缓存。
```
beforeEach(() => {
  jest.resetModules();
});

test('works', () => {
  const sum = require('../sum');
});

test('works too', () => {
  const sum = require('../sum');
  // 这两个sum是来自于sum模块的不同复制。
});
```

## jest.doMock(moduleName, factory, options)
当使用`babel-jest`时， 使用`mock`会自动被提升至代码顶部去声明，如果你想避免这个行为，使用该方法。
```
beforeEach(() => {
  jest.resetModules();
});

test('moduleName 1', () => {
  jest.doMock('../moduleName', () => {
    return jest.fn(() => 1);
  });
  const moduleName = require('../moduleName');
  expect(moduleName()).toEqual(1);
});

test('moduleName 2', () => {
  jest.doMock('../moduleName', () => {
    return jest.fn(() => 2);
  });
  const moduleName = require('../moduleName');
  expect(moduleName()).toEqual(2);
});
```
##jest.dontMock(moduleName)

当使用`babel-jest`时， 使用`unmock`会自动被提升至代码顶部去声明，如果你想避免这个行为，使用该方法。

##jest.clearAllMocks()

清除所有的mock的 **calls** 和 **instances** 属性，等同于调用每个`mock function`的`mockClear()` 方法
Note: `jest.fn().mock: { calls: [], instances: [], timestamps: [] }`

## jest.resetAllMocks()

重置所有的mock的状态，等同于调用每个`mock function`的`mockReset()`方法

## jest.spyOn(object, methodName)

类似于jest.fn创建一个`mock funtion`, 但也跟踪调用`object[methodName]`,该方法return返回一个`Jest mock function`

Note: 一般来说, `jest.spyOn`也叫监听方法（`spied method`）. 这是不同于其他测试库的行为. 如果你想重写它的实现方法,你可以用`jest.spyOn(object, methodName).mockImplementation(() => customImplementation)` 或者 `object[methodName] = jest.fn(() => customImplementation)`;

Note: 只能在Jest 19.0.0+使用

Example:
```
const video = {
  play() {
    return true;
  },
};

module.exports = video;
```
Example test:
```
const video = require('./video');

test('plays video', () => {
  const spy = jest.spyOn(video, 'play');
  const isPlaying = video.play();

  expect(spy).toHaveBeenCalled();
  expect(isPlaying).toBe(true);

  spy.mockReset();
  spy.mockRestore();
});
```