## .simulate(event[, mock]) => Self

参数
event: 触发的事件（例如点击事件: `'click'`）
mock: 传入事件的参数（例如：`wrapper('change', {target: { value: '3'}})`）

Note:
例如在antd中，onChange传入的不是event事件对象，而是直接传入一个值，那么应该这样写：
`wrapper('change', 3)`
