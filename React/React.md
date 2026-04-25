
[todo專案練習範例](https://reactsimpletodo.darenge.net/)
[[React Hook]]

> 組件上設定key值，是用來宣告該組件的唯一值，讓React知道該組件與其他的組件是不同的

## 父組件丟入至子組件的function，可設定為有輸入參數的

App.js：
```jsx
import { useState } from 'react';
import ContactList from './ContactList.js';

export default function Messenger() {
  const [to, setTo] = useState(contacts[0]);
  return (
    <div>
      <ContactList
        contacts={contacts}
        
        //丟入至子組件的function，contact為輸入參數
        onSelect={contact => setTo(contact)} 
      />
    </div>
  )
}

const contacts = [
  { id: 0, name: 'Taylor', email: 'taylor@mail.com' },
  { id: 1, name: 'Alice', email: 'alice@mail.com' },
  { id: 2, name: 'Bob', email: 'bob@mail.com' }
];
```

ContactList.js
```jsx
export default function ContactList({
  contacts,
  onSelect
}) {
  return (
    <section className="contact-list">
      <ul>
        {contacts.map(contact =>
          <li key={contact.id}>
            <button onClick={() => {
              onSelect(contact);
            }}>
              {contact.name}
            </button>
          </li>
        )}
      </ul>
    </section>
  );
}
```

## 透過`children`取得子組件

父組件(TaskProvider.jsx)，請忽略context：
```jsx
import { DispatchContext, TasksContext } from "./TaskContext";

function TaskProvider({ children }) {
  return (
    <>
      <TasksContext>
        <DispatchContext>{children}</DispatchContext>
      </TasksContext>
    </>
  );
}

export default TaskProvider;
```

TaskProvider 帶入子組件：
```jsx
import AddTask from "./AddTask";
import TaskList from "./TaskList";
import TaskProvider from "./TaskProvider";

function App() {
  return (
    //<TaskProvider></TaskProvider> 標籤內的組件就是children
    <>
      <TaskProvider>
        <h1>布拉格的行程安排</h1>
        <AddTask />
        <TaskList />
      </TaskProvider>
    </>
  );
}

export default App;
```

#### 進階寫法：讓函式執行後回傳另一個函式（可跳過）
```jsx
// handleClick('increment') 執行的時候實際上是回傳一個 type 為 increment 的函式
const handleClick = (type) => {
  return function () {
    if (type === 'increment') {
      setCount(count + 1);
    }
    if (type === 'decrement') {
      setCount(count - 1);
    }
  };
};

return ( 
<div className="container"> 
	<div className="chevron chevron-up" 
		onClick={handleClick('increment')} 
		style={{ visibility: count >= 10 && 'hidden', }} />
		 
		 {/* ... */}
		 
	<div className="chevron chevron-down" 
		onClick={handleClick('decrement')} 
		style={{ visibility: count <= 0 && 'hidden', }} /> 
</div> );
```