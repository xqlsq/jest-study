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