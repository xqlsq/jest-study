# 搭建项目遇到的一些问题

## 1、tslint报错

使用`ts|tsx`写测试用例tslint会报错，找不到 `describ` 、`it`、`test`、`expect`等,这是因为没有安装相应的jest的ts版本
```
cnpm install @types/jest --save-dev
```
就没有报错了！bingo！

## 2、mock、unmock提升

`jest.mock()、jest.unmock()` 会类似于var 生命变量一样。不管写在哪，编译的时候会变量提升到文件最顶部。

## jest 执行顺序

对于每个测试文件。jest貌似是随机顺序执行每个文件（测试几次发现毫无规律）。
但是进入测试文件，jest会严格的依次执行代码。如果遇到正确书写的异步代码，也会等待（等待的最大时长为5000ms）异步结束返回再执行下面的代码。（不信自己测试）

5000ms: Timeout - Async callback was not invoked within the 5000ms timeout specified by jest.setTimeout.