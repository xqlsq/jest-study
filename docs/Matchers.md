# 匹配器

## 匹配器(Matchers)

Jest 使用匹配器（matchers）让你用不同的方式来测试值。

### 1.普通匹配器

最简单的测试值的方法是看是否精确匹配。

```
test('two plus two is four', () => {
  expect(2 + 2).toBe(4);
});
```

toBe 使用 `Object.is` 来测试是否完全相等。如果你想检查某个对象的值，请改用 `toEqual`。

```
test('object assignment', () => {
  const data = {one: 1};
  data['two'] = 2;
  expect(data).toEqual({one: 1, two: 2});
});
```
`toEqual` **递归检查**对象或数组的每个字段。

您还可以测试相反的匹配︰

```
test('adding positive numbers is not zero', () => {
  for (let a = 1; a < 10; a++) {
    for (let b = 1; b < 10; b++) {
      expect(a + b).not.toBe(0);
    }
  }
});
```

在测试中，你有时需要区分 undefined、 null，和 false，但有时你又不需要区分。 Jest 里有些东西能帮助你明确地知道你想要什么。

* toBeNull 只匹配 null
* toBeUndefined 只匹配 undefined
* toBeDefined 与 toBeUndefined 相反
* toBeTruthy 匹配任何 if 语句为真
* toBeFalsy 匹配任何 if 语句为假

例如：
```
test('null', () => {
  const n = null;
  expect(n).toBeNull();
  expect(n).toBeDefined();
  expect(n).not.toBeUndefined();
  expect(n).not.toBeTruthy();
  expect(n).toBeFalsy();
});

test('zero', () => {
  const z = 0;
  expect(z).not.toBeNull();
  expect(z).toBeDefined();
  expect(z).not.toBeUndefined();
  expect(z).not.toBeTruthy();
  expect(z).toBeFalsy();
});
```
您应该使用匹配器最精确地对应您的代码想做的事。

#### 数字
大多数的比较数字有等价的匹配器。
```
test('two plus two', () => {
  const value = 2 + 2;
  expect(value).toBeGreaterThan(3);
  expect(value).toBeGreaterThanOrEqual(3.5);
  expect(value).toBeLessThan(5);
  expect(value).toBeLessThanOrEqual(4.5);

  // toBe and toEqual are equivalent for numbers
  expect(value).toBe(4);
  expect(value).toEqual(4);
});
```
对于比较浮点数的相等，应该使用 toBeCloseTo 而不是 toEqual，因为你不希望测试取决于一个小小的舍入误差。
```
test('两个浮点数字相加', () => {
  const value = 0.1 + 0.2;
  //expect(value).toBe(0.3);           这句会报错，因为浮点数有舍入误差
  expect(value).toBeCloseTo(0.3); // 这句可以运行
});
```
#### 字符串
您可以检查对具有 toMatch 正则表达式的字符串︰
```
test('there is no I in team', () => {
  expect('team').not.toMatch(/I/);
});

test('but there is a "stop" in Christoph', () => {
  expect('Christoph').toMatch(/stop/);
});
```
#### 数组
你可以检查数组是否包含特定子项使用 toContain︰

const shoppingList = [
  'diapers',
  'kleenex',
  'trash bags',
  'paper towels',
  'beer',
];

test('购物清单（shopping list）里面有啤酒（beer）', () => {
  expect(shoppingList).toContain('beer');
});

#### 例外

如果你要测试的特定函数会在调用时抛出一个错误，可以使用 toThrow。
```
function compileAndroidCode() {
  throw new ConfigError('you are using the wrong JDK');
}

test('compiling android goes as expected', () => {
  expect(compileAndroidCode).toThrow();
  expect(compileAndroidCode).toThrow(ConfigError);

  // You can also use the exact error message or a regexp
  expect(compileAndroidCode).toThrow('you are using the wrong JDK');
  expect(compileAndroidCode).toThrow(/JDK/);
});
```
**注意：**使用toThrow匹配器的时候expect里面的参数是一个函数，而不是函数执行的结果。可以参照如下写法：
```
expect(() => func()).toThrow();
```
`func` 为你需要执行的函数