>更新陣列時，需以會回傳一個新的陣列的function為主，不要直接修改陣列內容，這樣會無法更新state值(陣列)

|      | 避免使用 (会改变原始数组)             | 推荐使用 (会返回一个新数组）                                                                                             |
| ---- | -------------------------- | ----------------------------------------------------------------------------------------------------------- |
| 添加元素 | `push`，`unshift`           | `concat`，`[...arr]` 展开语法（[例子](https://zh-hans.react.dev/learn/updating-arrays-in-state#adding-to-an-array)） |
| 删除元素 | `pop`，`shift`，`splice`     | `filter`，`slice`（[例子](https://zh-hans.react.dev/learn/updating-arrays-in-state#removing-from-an-array)）     |
| 替换元素 | `splice`，`arr[i] = ...` 赋值 | `map`（[例子](https://zh-hans.react.dev/learn/updating-arrays-in-state#replacing-items-in-an-array)）           |
| 排序   | `reverse`，`sort`           | 先将数组复制一份（[例子](https://zh-hans.react.dev/learn/updating-arrays-in-state#making-other-changes-to-an-array)）   |

1. 新增陣列元素：
```jsx
const [todoItem, setTodoItem] = useState([
    { id: 1, content: "打掃廁所" },
    { id: 2, content: "洗衣服" },
    { id: 3, content: "洗碗" },
  ]);

//新增陣列元素
const addTodoItem = function (content) {
	setTodoItem([...todoItem, { id: Math.random(), content }]);
};
```

2. 刪除陣列元素：
```jsx
const [artists, setArtists] = useState([
  { id: 0, name: 'Marta Colvin Andrade' },
  { id: 1, name: 'Lamidi Olonade Fakeye'},
  { id: 2, name: 'Louise Nevelson'},
]);

fucntion delete(id){
	//.filter會回傳一個新的陣列
	setArtists(artists.filter(a => a.id !== id));
}
```

3. 逐筆輸出或更新陣列元素：
```jsx
let initialShapes = [
  { id: 0, type: 'circle', x: 50, y: 100 },
  { id: 1, type: 'square', x: 150, y: 100 },
  { id: 2, type: 'circle', x: 250, y: 100 },
];

const [shapes, setShapes] = useState(
    initialShapes
);
  
function handleClick() {

    //.map可逐筆輸出陣列內容，亦可將內容作轉換或更新後輸出
    const nextShapes = shapes.map(shape => {
      if (shape.type === 'square') {
        // 直接輸出
        return shape;
      } else {
        // 更新陣列元素
        return {
          ...shape,
          y: shape.y + 50,
        };
      }
    });
    
    // 使用新的数组进行重渲染
    setShapes(nextShapes);
  }
```

4. 在指定位置插入陣列元素：
```jsx
let nextId = 3;
const initialArtists = [
  { id: 0, name: 'Marta Colvin Andrade' },
  { id: 1, name: 'Lamidi Olonade Fakeye'},
  { id: 2, name: 'Louise Nevelson'},
];

function handleClick(name) {
    const insertAt = 1; // 可能是任何索引
    const nextArtists = [
      // 插入点之前的元素：
      ...artists.slice(0, insertAt),
      // 新的元素：
      { id: nextId++, name: name },
      // 插入点之后的元素：
      ...artists.slice(insertAt)
    ];
    setArtists(nextArtists);
  }
```

5. 複製陣列：
> 先複製一個新的陣列出來後，再進行陣列排序、翻轉或更新陣列元素(object)

```jsx
const initialList = [
  { id: 0, title: 'Big Bellies' },
  { id: 1, title: 'Lunar Landscape' },
  { id: 2, title: 'Terracotta Army' },
];

const [list, setList] = useState(initialList);

//複製一個陣列存放至nextList
const nextList = [...list];

//翻轉陣列
nextList.reverse();
setList(nextList);
```

6. 更新陣列內的object：
```jsx
const initialList = [
  { id: 0, title: 'Big Bellies', seen: false },
  { id: 1, title: 'Lunar Landscape', seen: false },
  { id: 2, title: 'Terracotta Army', seen: true },
];

const [myList, setMyList] = useState(initialList);

function handleToggleMyList(artworkId, nextSeen) {
	setMyList(myList.map(artwork => {  
		if (artwork.id === artworkId) {  
			// 创建包含变更的*新*对象  
			return { ...artwork, seen: nextSeen };  
		} else {  
			// 没有变更  
			return artwork;  
		}  
	}));    
}
```

7. 承上點，使用Immer套件更新陣列內的object：
```jsx
import { useImmer } from 'use-immer';

const initialList = [
  { id: 0, title: 'Big Bellies', seen: false },
  { id: 1, title: 'Lunar Landscape', seen: false },
  { id: 2, title: 'Terracotta Army', seen: true },
];

const [myList, updateMyList] = useImmer(initialList);

function handleToggleMyList(id, nextSeen) {
	//draft=陣列本身
	updateMyList(draft => {
	  const artwork = draft.find(a =>
		a.id === id
	  );
	  artwork.seen = nextSeen;
	});
}
```