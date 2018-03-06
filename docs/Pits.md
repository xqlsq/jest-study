# 搭建项目遇到的一些问题

## 1、tslint报错

使用`ts|tsx`写测试用例tslint会报错，找不到 `describ` 、`it`、`test`、`expect`等,这是因为没有安装相应的jest的ts版本
```
cnpm install @types/jest --save-dev
```
就没有报错了！bingo！

## 2、mock、unmock提升

`jest.mock()、jest.unmock()` 会类似于var 生命变量一样。不管写在哪，编译的时候会变量提升到文件最顶部。