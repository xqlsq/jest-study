# expect

## expect.any(constructor)
匹配任何由该构造器生成的实例。
You can use it inside toEqual or toBeCalledWith instead of a literal value. For example, if you want to check that a mock function is called with a number:
```
function randocall(fn) {
  return fn(Math.floor(Math.random() * 6 + 1));
}

test('randocall calls its callback with a number', () => {
  const mock = jest.fn();
  randocall(mock);
  expect(mock).toBeCalledWith(expect.any(Number));
});
```