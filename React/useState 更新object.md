
1. 完整更新
```jsx
const [person, setPerson] = useState({
    firstName: 'Barbara',
    lastName: 'Hepworth',
    email: 'bhepworth@sculpture.com'
  });

setPerson({
  firstName: e.target.value, // 从 input 中获取新的 first name
  lastName: person.lastName,
  email: person.email
});
```

2. 可使用展開語法，進行部分更新：
```jsx
setPerson({
  ...person, // 复制上一个 person 中的所有字段
  firstName: e.target.value // 但是覆盖 firstName 字段 
});
```

3. 透過展開語法，針對object內的object進行部分更新：
```jsx
const [person, setPerson] = useState({
  name: 'Niki de Saint Phalle',
  artwork: {
    title: 'Blue Nana',
    city: 'Hamburg',
    image: 'https://i.imgur.com/Sd1AgUOm.jpg',
  }
});

setPerson({
  ...person, // 复制其它字段的数据 
  artwork: { // 替换 artwork 字段 
    ...person.artwork, // 复制之前 person.artwork 中的数据
    city: 'New Delhi' // 但是将 city 的值替换为 New Delhi！
  }
});
```

4. 使用`Immer`套件更新object：
```jsx
import { useImmer } from 'use-immer';

const [person, updatePerson] = useImmer({
	name: 'Niki de Saint Phalle',
	artwork: {
	  title: 'Blue Nana',
	  city: 'Hamburg',
	  image: 'https://i.imgur.com/Sd1AgUOm.jpg',
	}
});

function handleNameChange(e) {
	//draft=object本身
    updatePerson(draft => {
      draft.name = e.target.value;
    });
  }

  function handleTitleChange(e) {
    //draft=object本身
    updatePerson(draft => {
      draft.artwork.title = e.target.value;
    });
  }

  function handleCityChange(e) {
    //draft=object本身
    updatePerson(draft => {
      draft.artwork.city = e.target.value;
    });
  }

  function handleImageChange(e) {
    //draft=object本身
    updatePerson(draft => {
      draft.artwork.image = e.target.value;
    });
  }
```
