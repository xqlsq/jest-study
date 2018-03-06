# Setup and Teardown

你会经常在写测试用例的时候遇到以下这种情况：在测试用例开始运行前，你需要做一些事情，准备工作。在测试用例结束后，也需要做些处理操作。

## 多数用例运行前需要做重复的准备工作

如果你有很多用例需要做重复的事情，那你可以用`beforeEach`和`afterEach`
```
var initialName;

beforeEach(() => {
    initialName = '徐博';
});

afterEach(() => {
    initialName = '';
});

test('city database has Vienna', () => {
    initialName = initialName + '好菜';
    expect(initialName).toBe('徐博好菜');
});

test('city database has San Juan', () => {
    initialName = initialName + '真心菜';
    expect(initialName).toBe('徐博真心菜');
});
```
`beforeEach` 和 `afterEach` 可以像`[TestAsyncCode](./TestAsyncCode.md)`一样操作异步代码。不过同时它也需要与`TestAsyncCode`一样注意使用promise的时候要return。

## 一次性做好准备工作

在某些情况下，你只需要在文件的开头setUp一次，当setUp是异步操作的时候，这可能会非常棘手，不过还好，jest 提供了`beforeAll` 和 `afterAll` 去处理这种情况。

打个比方：如果 `initializeCityDatabase` 和 `clearCityDatabase` return一个Promise，然后city的数据在不同的测试用例中使用，那我们就可以这样做：
```
beforeAll(() => {
  return initializeCityDatabase();
});

afterAll(() => {
  return clearCityDatabase();
});

test('city database has Vienna', () => {
  expect(isCity('Vienna')).toBeTruthy();
});

test('city database has San Juan', () => {
  expect(isCity('San Juan')).toBeTruthy();
});
```
## 作用域

