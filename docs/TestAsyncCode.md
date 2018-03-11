# 异步代码测试

在JavaScript中执行异步代码是很常见的。 当你运行异步代码时，Jest 需要知道它当前测试的异步代码什么时候完成，才可以运行到下一个测试。 Jest 有多种办法来处理这种情况。

## 回调

最常见的异步模式是回调函数。

举个例子，假设你有一个叫做 fetchData(callback) 的函数，可以去获取一些数据，拿到数据之后就会调用 callback(data)。 你想要这个返回的数据是一个 'peanut butter' 字符串。

默认情况下，Jest 测试走到它们运行期的结束阶段就算是结束了。这意味着这个测试 不会 按照你预期的那样运行：
```
// Don't do this!
test('the data is peanut butter', () => {
  function callback(data) {
    expect(data).toBe('peanut butter');
  }

  fetchData(callback);
});
```
问题在于这个测试在 fetchData 运行完的那一瞬间就算结束了，它根本就来不及去调用那个回调函数（callback）。

还有另一种形式的 test，解决此问题。 使用一个叫做 done 的单参数，而不是将测试放在一个没有参数的函数里。 Jest会等done回调函数执行结束后，结束测试。
```
test('the data is peanut butter', done => {
  function callback(data) {
    expect(data).toBe('peanut butter');
    done();
  }

  fetchData(callback);
});
```
如果 `done()` 没有被调用，测试就会失败，这也是你所希望发生的。

## Promises
如果您的代码使用 Promises，还有一个更简单的方法来处理异步测试。 只需要从您的测试返回一个 Promise, Jest 会等待这一Promise解析完成。 如果Promise被拒绝，则测试将自动失败。

比如，那段 fetchData 的代码，不要使用回调函数，而是返回一个 Promise，应该会解析出一个 'peanut butter' 的字符串。我们可以用下面的代码来测试它：

test('the data is peanut butter', () => {
  expect.assertions(1);
  return fetchData().then(data => {
    expect(data).toBe('peanut butter');
  });
});

一定要返回一个 Promise，如果你省略掉 return 语句，你的测试会在 fetchData 拿到数据之前就结束了。
如果你想要 Promise 被拒绝，使用 .catch 方法。 确保添加了`expect.assertions`来确定断言被调的个数。
否则一个fulfilled态的 Promise 不会让测试失败︰
```
test('the fetch fails with an error', () => {
  expect.assertions(1);
  return fetchData().catch(e => expect(e).toMatch('error'));
});
```

## Async/Await
或者，您可以在测试中使用 async 和 await。 若要编写 async 测试，只要在传给 test 的函数前面加上 async 关键字就行了。例如，可以用来测试相同的 fetchData 方案︰

test('the data is peanut butter', async () => {
  expect.assertions(1);
  const data = await fetchData();
  expect(data).toBe('peanut butter');
});

test('the fetch fails with an error', async () => {
  expect.assertions(1);
  try {
    await fetchData();
  } catch (e) {
    expect(e).toMatch('error');
  }
});
当然，你可以把 async 和 await 还有 .resolves 或 .rejects结合起来使用（仅用于 Jest 20.0.0+）。

test('the data is peanut butter', async () => {
  expect.assertions(1);
  await expect(fetchData()).resolves.toBe('peanut butter');
});

test('the fetch fails with an error', async () => {
  expect.assertions(1);
  await expect(fetchData()).rejects.toMatch('error');
});
在这些例子中，async 和 await 仅仅只是语法糖，其本身的逻辑和使用 Promise 的例子是等价的。

上述的多种形式中，没有哪种形式法会比其他形式更好。你可以在整个代码库中，甚至也可以在单个文件中结合地使用它们。这只取决于哪种形式更能使您的测试变得简单。