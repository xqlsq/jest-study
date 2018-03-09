Jest 默认的config:
```
exports.default = {
  automock: false,
  bail: false,
  browser: false,
  cache: true,
  cacheDirectory,
  changedFilesWithAncestor: false,
  clearMocks: false,
  coveragePathIgnorePatterns: [NODE_MODULES_REGEXP],
  coverageReporters: ['json', 'text', 'lcov', 'clover'],
  detectLeaks: false,
  expand: false,
  forceCoverageMatch: [],
  globalSetup: null,
  globalTeardown: null,
  globals: {},
  haste: {
    providesModuleNodeModules: []
  },
  moduleDirectories: ['node_modules'],
  moduleFileExtensions: ['js', 'json', 'jsx', 'node'],
  moduleNameMapper: {},
  modulePathIgnorePatterns: [],
  noStackTrace: false,
  notify: false,
  notifyMode: 'always',
  preset: null,
  resetMocks: false,
  resetModules: false,
  restoreMocks: false,
  runTestsByPath: false,
  runner: 'jest-runner',
  snapshotSerializers: [],
  testEnvironment: 'jest-environment-jsdom',
  testEnvironmentOptions: {},
  testFailureExitCode: 1,
  testLocationInResults: false,
  testMatch: ['**/__tests__/**/*.js?(x)', '**/?(*.)(spec|test).js?(x)'],
  testPathIgnorePatterns: [NODE_MODULES_REGEXP],
  testRegex: '',
  testResultsProcessor: null,
  testURL: 'about:blank',
  timers: 'real',
  transformIgnorePatterns: [NODE_MODULES_REGEXP],
  useStderr: false,
  verbose: null,
  watch: false,
  watchPathIgnorePatterns: [],
  watchman: true
};
```

## globalSetup [string]

此选项允许自定义设置全局模块，该模块`exports`异步函数，在所有的测试集开始前触发一次。

## globalTeardown [string]

此选项允许自定义拆除全局模块，该模块`exports`异步函数，在所有的测试集完成后触发一次。

