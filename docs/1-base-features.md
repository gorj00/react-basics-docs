# I. Base Features
_...return to React Docs [chapters](./readme.md)_

## 1 React Components Basics
### 1.1 JSX restrictions
- do not use in HTML **class** => use className `<div className="some-class">`,
- return only one **root element** in render method _(may be allowed in later versions of React to return multiples)_,
- may be handled with React's Fragment component (wraps JSX into one root element):
```jsx
import React from 'react';

render() {
    return (
        <div className="root-component">
            ... everything else
        </div>
    );
}

// or
import React, { Fragment } from 'react';

render() {
    return (
        <Fragment>
            ... everything else
        </Fragment>
    );
}
```

### 1.2 Functional Components
- component in the simplest form is a function returning JSX

Create:
```jsx
import React from 'react';

const person = (props) => {
    return <p>I'm a Person</p>;
}

export default person;
```

Use: 
- import this component elsewhere with a **capital** letter, since lowercase is reserved for HTML elements
```jsx
// other parent component
import Person from './Person/Person'

const parentComponent = (props) => {
    return (
        <div className="parent-component">
            <Person></Person>
            // or
            <Person />

            // considered as an HTML element and not a component
            <person></person> 
        </div>
    );
}
```

### 1.3 Class Components
- Renders JSX with **render()** method returning the JSX
```jsx  
import React, { Component } from 'react';

class Person extends Component {
 render() {
   return <p>My name is {this.props}</p>;
 }
} 
 
```

### 1.4 Stateful vs. Stateless Components
- **stateful component** manages state,
  - **_smart components_** - usually containers,
  - trying to limit their number,
  - clear flow of data and changes of state
- **stateless component** presents data, 
  - **_dummy components_** - usually pure presentational components,
  - as many as possible


## 2 Component Inputs – _props_ (setting info from outside)
### 2.1 Component's Attributes as Props Properties
#### 2.1.1 Setup Props
- In component implementation (parent component returning JSX), define the **components attributes** (_name_ and _age_ as examples below): 
```jsx
// ParentComponent.js
import Component from 'Component.js';

const parentComponent = () => {
    return (
        <div className="root-style">
            <Component name="John" age="25" />
        </div>
    );
}

``` 
- in component declaration (the component's file), provide the component function an **argument** of _props_ (name optional), which will contain the component attributes as the object properties,
- you render them with **curly braces** `{}`
```jsx
// Component.js
const component = (props) => {
    return (
        <p>I am {props.name} and I am {props.age} years old.</p>
    );
```
- <span style="color:red">important</span>: in **class components**, you access the attributes with **this.props** `<p>I am {this.props.name} and I am {this.props.age} years old.</p>`

#### 2.1.2 PropTypes
- To define and type all the props the component accepts,
- install **prop-types** package with: `npm install --save prop-types`,
- import **PropTypes** from the package in your component,
- after component definition, define a **propTypes** property of your component and **assign** it a JS object containing types for your prop over **PropTypes**
```jsx
// Person.js

    import React from 'react';
    import PropTypes from 'prop-types';

    // ... component definition

    Person.propTypes = {
        click: PropTypes.func,
        name: PropTypes.string,
        age: PropTypes.number,
        changed: PropTypes.func
    }

    export default Person;
```

### 2.2 Content Between Opening and Closing Tag – _children_ Property of _props_
- Place content between opening and closing tags while implementing a component: 
```jsx
// ParentComponent.js
import Component from 'Component.js';

const parentComponent = () => {
    return (
        <div className="root-style">
            <Component name="John" age="25">I am a developer.</Component>
        </div>
    );
}
```
- access this content in your component declaration and render it: 
```jsx
// Component.js
const component = (props) => {
    return (
        <div>
            <p>I am {props.name} and I am {props.age} years old.</p>
            <p>Message: {props.children}</p>
        </div>
    );
```

### 2.3 Passing method reference between components
- Useful for a child component trigerring a parent's method,
- see [section 5](#ref-between-components)

### 2.4 Data (Props-like) Across Multiple Nested Components – Context API
To prevent creating a deep **props chain** when sharing props across multiple nested components, use **Context API**, it is possible to use _Redux_ as an alternative.

- For Context API, go to [chapter 5](./5-context-api.md)

## 3 Class Component State (setting info from inside)
- Every component extending _React Component_ has a reserved **state** property which allows to define it from inside the component,
- this applies to **class components only**,
- from React 16.8+, it is possible to apply _state_ in functional components with **react hooks** (recommended approach: using functional components)

**Example**: 
```jsx
// People.js

import React, { Component } from 'react';
import Person from './Person'

class People extends Component {

    state = {
        persons: [
            { name: 'John', age: 25},
            { name: 'Anne', age: 22}
        ]
    }

    render () {
        return ( 
            <div className="people"> 
                <Person 
                    name={this.state.persons[0].name} 
                    age={this.state.persons[0].age} 
                />
                <Person 
                    name={this.state.persons[1].name} 
                    age={this.state.persons[1].age} 
                />
            </div>
        );
    }
}

export default People;
```
- every change of _state_ rerenders the component

### 3.1 Event Listeners
- Let's continue with the example above,
- add a button to switch names, **call** a component method (convention: ends with the word **Handler**),
- **example**: `<button onClick={this.switchNameHandler}>Switch Name</button>`
  - traditional Javascript events listeners are available, only with **camelCase** naming; therefore, _onclick_ becomes _onClick_, full list [here](https://reactjs.org/docs/events.html#supported-events),
  - call the method with _this_ object inside **curly braces**,
  - **do not** call the method with parentheses at the end such as this <del>`onClick={this.switchNameHandler()}`</del>, 
     - it would start the method on the initial component render and not on the event, 
     - you pass only a **reference** name of the method, not the method itself

### 3.2 State Manipulation
#### 3.2.1 setState()
- State **must not be mutated/changed** directly as this: 
```jsx
  switchNameHandler = () => {
    // ! NOT ALLOWED: 
    this.state.persons[0].name = 'Peter'; 
  }
```
- it **must** be mutated only with the **setState** method, it takes an object as an argument and it **merges** with the original state,
- therefore, it changes the part of the state defined in the passed object in the _setState_ method and it keeps the rest of the state untouched and it includes it in the updated state
```jsx
  state = {
    persons: [
      { name: 'John', age: 29},
      { name: 'Anne', age: 30}
    ],
    iWillBeKept: true,
  }

  switchNameHandler = () => {
    this.setState({
      persons: [
        { name: 'Max', age: 29},
        { name: 'Mia', age: 22}
      ]
    })
  }
```
- after clicking on the button with _switchNameHandler_ method reference, the new state will result in: 
```jsx
// value of state property:

persons: [
    { name: 'Max', age: 29}, // used to be John
    { name: 'Mia', age: 22} // used to be Anne and 30
],
iWillBeKept: true, // untouched and merged
```
#### 3.2.1 setState(prevState, props) – Change Depends on Previous State
- **warning**: if depending on an _older version_ of state (for example `changeCounter: this.state.changeCounter + 1`), it does not have to execute immediately, it is only "scheduled"; therefore, we work with _unexpected_ state (wrong counter number may occur),
- **solution**: provide _setState_ with arguments of **previous state** and **props**
```jsx
// May be faulty: 
//  this.setState({
//      changeCounter: this.state.changeCounter + 1
//  });

// Use instead: 
        this.setState((prevState, props) => {
            return {
                // use prevState
                changeCounter: prevState.changeCounter + 1
            };
    });
```

## 4 Functional Component State (setting info from inside with React hooks)
- React version 16.8+,
- import hooks (starting with _use_ keyword) from React `import { useState } from 'react'`,
- **pass** your initial state to **useState** method, it will return an array with exactly two elements: 
  - **#1 element**: current state, 
  - **#2 element**: function allowing to update the state
- store function handlers in _const_ variables,
- **differences from class component state**:
    -  <span style="color:red">important</span>: _useState_ hook doesn't merge the updated state with the original one, it replaces it!
    - in functional component state, it is possible to us _useState_ method call **multiple times**

**Example**: 
```jsx
const people = props => {
    // ES6 array destructuring
    // peopleState - current state
    // setPeopleState - function to update the state
    const [peopleState, setPeopleState] = useState({
        persons: [
            { name: 'John', age: 29},
            { name: 'Anne', age: 30}
        ],
        // will be lost
        iWillBeKept: false,
    });

    // store function in a variable
    const switchNameHandler = () => {
        setPeopleState({
            persons: [
                { name: 'Max', age: 29},
                { name: 'Mia', age: 22}
            ]
        });
    }

    return ( 
        <div className="people"> 
            <button onClick={switchNameHandler}>Switch Name</button>
            <Person 
                name={peopleState.persons[0].name} 
                age={peopleState.persons[0].age} 
            />
            <Person 
                name={peopleState.persons[1].name} 
                age={peopleState.persons[1].age} 
            />
        </div>
    );
}
```

To **merge** states - use multiple _useState_ calls or update state with the entire current state along with the updates: 
```jsx
//OPTION 1: Multiple useState calls
  const [peopleState, setPeopleState] = useState({
    persons: [
        { name: 'John', age: 29},
        { name: 'Anne', age: 30}
    ],
    iWillBeMerged: true
  });

  const [mergeState, setMergeState] = useState(
    {...peopleState, iWillBeMerged: true}
    );



// OPTION 2: Updating with entire current state + updates
    const initialState = {
        persons: [
            { name: 'John', age: 29},
            { name: 'Anne', age: 30}
        ],
        iWillBeMerged: true
    };

    const [peopleState, setPeopleState] = useState(initialState);

    const switchNameHandler = () => {
    setPeopleState({
      // return the entire current state
      ...peopleState,
      // update persons array only
      persons: [
        { name: 'Max', age: 29},
        { name: 'Mia', age: 22}
      ]
    });
  }
```
<h2 id="ref-between-components">5 Passing Method Reference Between Components</h2>
- Useful for a child component trigerring a parent's method

- In **parent** component, define a property on a child component (input) and assign it a **parent method reference name**:
```jsx
// People.js
class People extends Component {

    state = {
        persons: [
            { name: 'John', age: 29},
            { name: 'Anne', age: 30}
        ],
        iWillBeKept: true,
    }

    switchNameHandler = () => {
        this.setState({
            persons: [
                { name: 'Max', age: 29},
                { name: 'Mia', age: 22}
            ]
        })
    }

    render () {
        return ( 
            <div className="people"> 
                // switchNameHandler only for functional components
                <button onClick={this.switchNameHandler}>Switch Name</button>
                <Person 
                    name={this.state.persons[0].name} 
                    age={this.state.persons[0].age} 

                    // property with method reference name
                    // switchNameHandler only for functional components
                    clickReference={this.switchNameHandler}

                />
                <Person 
                    name={this.state.persons[1].name} 
                    age={this.state.persons[1].age} 
                />
            </div>
        );
    }
}
```
- in **child** component, listen to the event (_onClick_ in this example) on the JSX element and access the parent method reference name on the **props object**:
```jsx
// Person.js
const person = (props) => {
    return (
        <div>
            <p onClick={props.clickReference}>I am {props.name} and I am {props.age} years old.</p>
            <p>Message: {props.children}</p>
        </div>
    );
};
export default person;
```

### 5.1 Passing Values in Class Components
#### 5.1.1 Using bind()
- Bind your method call to reference the class (this) with **bind()** method on your reference method call,
- next, list what will be **passed** to the function in the _bind()_ method
#### 5.1.2 Using arrow function
- Change method call reference into an **arrow function** returning method **call**,
- bind() method is preferred due to React rerendering too often
```jsx
// People.js
class People extends Component {

    state = {
        persons: [
            { name: 'John', age: 29},
            { name: 'Anne', age: 30}
        ],
        iWillBeKept: true,
    }

    // receives a new name
    switchNameHandler = newName => {
        this.setState({
            persons: [
                { name: 'Max', age: 29},
                { name: 'Mia', age: 22}
            ]
        })
    }

    render () {
        return ( 
            <div className="people"> 
                // switchNameHandler only for functional components
                <button onClick={this.switchNameHandler}>Switch Name</button>
                <Person 
                    name={this.state.persons[0].name} 
                    age={this.state.persons[0].age} 
                    // using bind()
                    clickReference={this.switchNameHandler.bind(this, 'Jack')}
                />
                <Person 
                    name={this.state.persons[1].name} 
                    age={this.state.persons[1].age}
                    // using arrow function 
                    clickReference={() => this.switchNameHandler('Jack')}
                />
            </div>
        );
    }
}
```
### 5.2 Passing Values in Functional Components
#### 5.2.1 Using arrow function
- Change method call reference into an arrow function returning method call,
- assuming the example of class component arrow function passing a value above, **example** of functional component implementation: 
```jsx
// ...component code

// ... inside return()
<Person 
    name={peopleState.persons[1].name} 
    age={peopleState.persons[1].age}
    // using arrow function 
    clickReference={() => switchNameHandler('Jack')}
/>
```

## 6 Two-way Binding
### 6.1 Class Components
- Create an **input** 
  - with _onChange_ event listener that listens with a parent's method that automatically accepts an **event** argument,
  - with _value_ gotten from props
  
```jsx
// Person.js
const person = (props) => {
    return (
        <div>
            <p onClick={props.clickReference}>I am {props.name} and I am {props.age} years old.</p>
            <p>Message: {props.children}</p>

            // automatically event object passed
            <input type="text" onChange={props.changed} value={props.name}>
        </div>
    );
};
export default person;
```
- create a method that accepts an **event** parameter,
- access the event's **value** and update the state with it,
- **pass this method** as a reference method name to the component's properties:
```jsx
// People.js
class People extends Component {

    state = {
        persons: [
            { name: 'John', age: 29},
            { name: 'Anne', age: 30}
        ],
        iWillBeKept: true,
    }

    switchNameHandler = () => {
        this.setState({
            persons: [
                { name: 'Max', age: 29},
                { name: 'Mia', age: 22}
            ]
        })
    }

    // receiving an event object
    nameChangeHandler = event => {
        this.setState({
            persons: [
                { name: event.target.value, age: 29},
                { name: 'Mia', age: 22}
            ]
        })
    }

    render () {
        return ( 
            <div className="people"> 
                // switchNameHandler only for functional components
                <button onClick={this.switchNameHandler}>Switch Name</button>
                <Person 
                    name={this.state.persons[0].name} 
                    age={this.state.persons[0].age} 

                    // changes on input changes
                    changed={this.nameChangeHandler}

                />
                <Person 
                    name={this.state.persons[1].name} 
                    age={this.state.persons[1].age}
                />
            </div>
        );
    }
}
```
### 6.2 Functional Components
- Use _useState_ React hook, using the example above, simplified, from class components:
```jsx
// People.js

// ... code

const [peopleState, setPeopleState] = useState(initialState);

const nameChangeHandler = event => {
    setPeopleState({
        ...peopleState,
        persons: [
            { name: event.target.value, age: 29},
            { name: 'Anne', age: 30}
        ]
    });
}

return ( 
    <div className="App"> 
        <header className="App-header">
        <button onClick={switchNameHandler}>Switch Name</button>
            <Person 
                name={peopleState.persons[0].name} 
                age={peopleState.persons[0].age} 

                // setting property with reference method
                change={nameChangeHandler}

            />
            <Person 
                name={peopleState.persons[1].name} 
                age={peopleState.persons[1].age}
            />
        </header>
    </div>
);
```

## 7 HOC – Higher Level Component
- HIC **wraps** another component, may add to the wrapped component:
    - styling,
    - additional JSX structure,
    - logic
- convention of naming - **With** at the beginning `WithClass.js`,
- the same HOC can be written in two ways:
    - **JSX implementation**: suitable more for _styling_ and _additional JSX structure_ cases,
    - **export implementation**: suitable more for _logic_ cases,
- **examples**: Radium, React.Fragment, component setting a div with class on a component

### 7.1 HOC – JSX Implementation
Component providing a class div **example**: 
```jsx
// WithClass.js

    import React from 'react';

    const WithClass = props => (
        <div className={props.classes}>{props.children}</div>
    );

    export default WithClass;
```
```jsx
// WrappedComponent.js

    import WithClass from './WithClass';

    // ... component code
    return(
        <WithClass classes={styleObject['text-in-bold-class']}>
            // ... WrappedComponent JSX
        </WithClass>
    );

    export default WrappedComponent;
```
### 7.2 HOC – Export Implementation
- A regular JS function, where its arguments must be:
    - **reference** to the **wrapped component**,
    - **arguments specific** to this HOC (can be multiple),
- return a **function body**, where you:
    - return a **functional component**,
    - handle **unknown props** with spread operator inside JS object `{...props}`

Component providing a class div **example**: 
```jsx
// withClass.js

    import React from 'react';

    const withClass = (ComponentRef, className) => {
        return props => (
            <div className={className}>
                <ComponentRef {...props}/>
            </div>
        );
    };

    export default withClass;
```
```jsx
// WrappedComponent.js

    import withClass from './withClass';

    // ... component code
    return(
            // ... WrappedComponent JSX
    );

    export default withClass(WrappedComponent, styleObject['text-in-bold-class']);
```
 or Component
## 8 Reference (Element and Component Manipulation)
- Manipulation of the element or component,
- use **ref**, a special property placed on the element or component

### 8.1 Class Component Reference
### 8.1.1 Reference with _passed function on element's property_
- older approach,
- pass an **anonymous function** with an argument of the element it is placed on,
- store it in a **global** property - to have access to it anywhere in your app
```jsx
import React, { Component } from 'react';
class Person extends Component {
    // ... component code

    componentDidMount() {
        this.inputElement.focus();
    }

    render(
        return(
            <Aux>
                <input 
                    type="text"
                    value={this.props.name}
                    onChange={this.props.changed}
                    ref={inputEl => {this.inputElement = inputEl}}
                />
            </Aux>
        );
    ) ;
}
```
### 8.1.2 Reference with _createRef()_
- modern approach, 
- uses **constructor** and React's **createRef()** method,
- access the element or component on **current** property of the reference

```jsx
import React, { Component } from 'react';
class Person extends Component {
    constructor(props) {
        super(props);
        this.inputElementRef = React.createRef();
    }

    // ... component code

    componentDidMount() {
        // current property of the reference
        this.inputElementRef.current.focus();
    }

    render(
        return(
            <Aux>
                <input 
                    type="text"
                    value={this.props.name}
                    onChange={this.props.changed}
                    ref={this.inputElementRef}
                />
            </Aux>
        );
    ) ;
}
```
### 8.2 Functional Component Reference
- Only modern approach is possible with the use of **useRef()** React hook,
- **import** _useRef_ from react,
- create a constant, store _useRef()_,
- you can store **initial values**,
- **manipulate** the referenced element with the use of _useEffect()_ hook (because we need the manipulation happen _after_ the JSX is rendered and the reference is set up),
- access the element or component on **current** property of the reference
```jsx
import React, { useEffect, useRef } from 'react';

const cockpit = props => {
    const toggleBtnRef = useRef(null);

    // Runs on create component only
    useEffect(() => {
        toggleBtnRef.current.click();
    }, []);

    return(
            <Aux>
                <button ref={toggleBtnRef}>
                    Click me!
                </button>
            </Aux>
        );
}
```