# React Native

## About

### Quick look

- Quicker development
- Requires more expertise to ensure high performance
- Harder code design and performance on different versions of each platform

### How it work?

[here](https://blog.logrocket.com/exploring-react-natives-new-architecture/#:~:text=In%20the%20current%20architecture%2C%20when,modules%20and%20handles%20UI%20rendering.)

[update to new architecture](https://reactnative.dev/blog/2022/03/15/an-update-on-the-new-architecture-rollout)

## Fundamentals

### Component

Render React element use JSX

#### [Component Lifecycle](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)

1. **Mounting**

These methods are called in the following order when an instance of a component is being created and inserted into.

- constructor()
- static getDerivedStateFromProps() [rare used](#rare-used)
- render()
- componentDidMount(): invoked immediately after a component is mounted. If re-render is triggerd in this method, render() will be called twice before the browser update the screen

1. **Updating**

An update can be caused by changes to props or state. These methods are called in the following order when a component is being re-rendered:

- static getDerivedStateFromProps() [rare used](#rare-used)
- shouldComponentUpdate() [rare used](#rare-used)
- render()
- getSnapshotBeforeUpdate() [rare used](#rare-used)
- componentDidUpdate():

1. **Unmounting**

This method is called when a component is being removed:

- componentWillUnmount()

1. **Error handling**

These methods are called when there is an error during rendering, in a lifecycle method, or in the constructor of any child component.

- static getDerivedStateFromError()
- componentDidCatch()

<a id="rare-used"></a>

[**Rare used method**](https://reactjs.org/docs/react-component.html#rarely-used-lifecycle-methods)

### JSX

[More](https://reactjs.org/docs/jsx-in-depth.html)

- Wrapping any Js expression in the curly braces (2+2, user.firstName, getFirstName(user),...)

```js
const name = 'Josh Perez';
const element = <Text>Hello, {name}</Text>;
```

- Babel compiles JSX down to React.createElement() calls.

```jsx
<MyButton color="blue" shadowSize={2}>
    Click Me
</MyButton>

//compiles to
React.createElement(
    MyButton,
    {color: 'blue', shadowSize: 2},
    'Click Me'
)
```

- User-Defined Components Must be Capitalized. React think `<hello>` is an HTML tag because its not capitalied`</hello>`

### Props

- Props object

```jsx
return <Greeting firstName="Ben" lastName="Hector" />
//equal
const props = {firstName: 'Ben', lastName: 'Hector'};
return <Greeting {...props} />;
```

### State

- State is like a component's data storage that handle data change over time or come from user interaction.

- pattern : [`<getter>`, `<setter>`] = useState(`<initialvalue>`)

- **Note:** setter is asynchronous and if u need previous state to determine next one u will need to call with callback as below:

```js
const [num, setNum] = useState(0)
useEffect(() => {   //manage lifecycle
    //on component mount
    const workId = setInterval(() => setNum(num => num + 1), 1000)
    return () => { //on component unmount
        clearInterval(workId)
    }
}, [])
```

## Debugging

- Ignore logs

```js
import { LogBox } from 'react-native';

// Ignore log notification by message:
LogBox.ignoreLogs(['Warning: ...']);

// Ignore all log notifications:
LogBox.ignoreAllLogs();
```

- Use react-devtools: run react-devtools in terminal and show inspector in dev menu.
