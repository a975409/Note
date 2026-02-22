>以use開頭命名的函數，稱之為Hook；Hook不能放在條件式或迴圈內，只能放在該`組件`的最上層

[React 内置 Hook](https://zh-hans.react.dev/reference/react/hooks#)

| useRef                                               | useState                                                                                      |
| ---------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| `useRef(initialValue)`返回 `{ current: initialValue }` | `useState(initialValue)` 返回 state 变量的当前值和一个 state 设置函数 ( `[value, setValue]`)                 |
| 更改时不会触发重新渲染                                          | 更改时触发重新渲染。                                                                                    |
| 可变 —— 你可以在渲染过程之外修改和更新 `current` 的值。                  | “不可变” —— 你必须使用 state 设置函数来修改 state 变量，从而排队重新渲染。                                               |
| 你不应在渲染期间读取（或写入） `current` 值。                         | 你可以随时读取 state。但是，每次渲染都有自己不变的 state [快照](https://zh-hans.react.dev/learn/state-as-a-snapshot)。 |

[[useState]]
[[useReducer]]
[[useContext]]
[[useRef]]
[[useEffect]]
