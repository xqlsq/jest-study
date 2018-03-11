# Setup and Teardown

你会经常在写测试用例的时候遇到以下这种情况：在测试用例开始运行前，你需要做一些事情，准备工作。在测试用例结束后，也需要做些处理操作。

## 多数用例运行前需要做重复的准备工作

如果你有很多用例需要做重复的事情，那你可以用`beforeEach`和`afterEach`
```
var initialName = '';

beforeEach(() => {
    initialName = '代码';
});

afterEach(() => {
    initialName = '';
});

test('city database has Vienna', () => {
    initialName = initialName + '有点烂';
    expect(initialName).toBe('代码有点烂');
});

test('city database has San Juan', () => {
    initialName = initialName + '真心烂';
    expect(initialName).toBe('代码真心烂');
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

通常情况，the `before` 和 `after` 会在这个文件下的每个测试用例都生效，不过你可以组织测试用例放入`describe`作用域里面，这样the `before` 和 `after` 只会在该`describe`里面的所有用例生效.
```
beforeEach(() => {
  console.log('top beforeEach');
});

test('top test', () => {
  console.log('top test);
});

describe('describe block', () => {
  beforeEach(() => {
    console.log('describe block beforeEach');
  });

  test('describe block test', () => {
    console.log('describe block test');
  });
});
```

测试文件顶层的`beforeEach`优先级高于`describe`的`beforeEach`. 它可能有助于说明所有钩子的执行顺序。
```
beforeAll(() => console.log('1 - beforeAll'));
afterAll(() => console.log('1 - afterAll'));
beforeEach(() => console.log('1 - beforeEach'));
afterEach(() => console.log('1 - afterEach'));
test('', () => console.log('1 - test'));
describe('Scoped / Nested block', () => {
  beforeAll(() => console.log('2 - beforeAll'));
  afterAll(() => console.log('2 - afterAll'));
  beforeEach(() => console.log('2 - beforeEach'));
  afterEach(() => console.log('2 - afterEach'));
  test('', () => console.log('2 - test'));
});
// 1 - beforeAll
// 1 - beforeEach
// 1 - test
// 1 - afterEach
// 2 - beforeAll
// 1 - beforeEach
// 2 - beforeEach
// 2 - test
// 2 - afterEach
// 1 - afterEach
// 2 - afterAll
// 1 - afterAll
  ```

  Jest在真正执行用例前，`describe`里面除了test内部代码，全部都会被先执行。这是另一个使用`before*` 和 `after* `操作的原因，一旦`describe`块完成，默认情况下，Jest会按照收集阶段的顺序，依次运行所有测试。直到测试完成才会继续下一个用例
```
describe('outer', () => {
    console.log('describe outer-a'); // 1
  
    describe('describe inner 1', () => {
      console.log('describe inner 1');// 2
      test('test 1', () => {
        console.log('test for describe inner 1');//6
        expect(true).toEqual(true);
      });
    });
  
    console.log('describe outer-b');//3
  
    test('test 1', () => {
      console.log('test for describe outer');//7
      expect(true).toEqual(true);
    });
  
    describe('describe inner 2', () => {
      console.log('describe inner 2');//4
      test('test for describe inner 2', () => {
        console.log('test for describe inner 2');//8
        expect(false).toEqual(false);
      });
    });
  
    console.log('describe outer-c');//5
  });
  ```
## 一些小小的建议

如果有一个用例失败，你要做的第一件事就是单独运行这个用例，你只需要在test方法上加入.only就可以实现了（describe.only也如此）。就像这样：`test.only()` `it.only`
```
test.only('this will be the only test that runs', () => {
  expect(true).toBe(false);
});

test('this test will not run', () => {
  expect('A').toBe('A');
});
```
如果你在一个很大的测试集里面有一个用例经常失败，但是单独测试是pass的，大概率可以确信是其他用例干扰了它，或许你清除一下 `beforeEach`带来的共享状态，就能fixed它，如果你不确认，你可以先打印数据查查问题。
