# IV. Lifecycle Hooks & React Hooks
_...return to React Docs [chapters](./readme.md)_

## 1 Information
**Lifecycle hooks** are available for **class components only**.

Use functional **react hooks** for **functional components** as an _alternative_ (_useState_ and _useEffect_).

## 2 Class Components – Lifecycle Hooks
- _Common ones are in bold..._

### 2.1 Component Creation
- **constructor(props)**
    - must call `super(props)` in the constructor,
    - setting up **initial state**,
    - do not use for side effects
- static getDerivedStateFromProps(props, state)
    - whenever props change, **sync** the state,
    - used very rarely
- **render()**
    - prepares and structures your **JSX code**,
    - do not use for side effects,
    - renders all **child components**
- **componentDidMount()**
    - use for **side effects**,
    - do not use for updating state (possible with promises)

**Examples**:
```jsx
import React, { Component } from 'react';

class Person extends Component {
        constructor(props) {
            super(props);

            console.log('[Person.js] constructor');
            this.state = { /*... state ...*/ };
        }

        /* 
        * State initialization alternative out of constructor 
        * It sets up constructor with props and this.state property automatically 
        */
        // state = { /*... state ...*/ };

        static getDerivedStateFromProps(props, state) {
            console.log('[Person.js] getDerivedStateFromProps', props);
        }

        render() {
            console.log('[Person.js] render');
            /* ... JSX code ...*/
        }

        componentDidMount() {
            console.log('[Person.js] componentDidMount');
            // http request for example
        }
}
```
### 2.2 Component Update
- static getDerivedStateFromProps(props, state)
    - initializing/updating (syncing) state for updated props from outside (for example change in a form control)
- shouldComponentUpdate(nextProps, nextState)
    - may cancel updating process,
    - decide whether to continue rerendering the component or not,
    - performance optimalization
- **render()**
    - same as in _component creation_
- getSnapshotBeforeUpdate(prevProps, prevState)
    - snapshot of previous state of the component,
    - rarely used for last-minute DOM operations
- **componentDidUpdate(prevProps, prevState, snapshot)**
    - after update, cause **side effects**,
    - **watch out** for infinite loops with http request (request => update component => request => ...)

**Examples**:
```jsx
import React, { Component } from 'react';

class Person extends Component {
        constructor(props) {
            super(props);

            console.log('[Person.js] constructor');
            this.state = { /*... state ...*/ };
        }

        static getDerivedStateFromProps(props, state) {
            console.log('[Person.js] getDerivedStateFromProps');
            return state;
        }

        shouldComponentUpdate(nextProps, nextState) {
            console.log('[Person.js] shouldComponentUpdate');
            // returns boolean, usually comparing props
            // true - will update, false won't update
            return true;
        }

        getSnapshotBeforeUpdate(prevProps, prevState) {
            console.log('[Person.js] getSnapshotBeforeUpdate');
            // returns snapshot value or null and passes to componentDidUpdate()
            // return null;
             return { message: 'From getSnapshotBeforeUpdate!'};
        }

        render() {
            console.log('[Person.js] render');
            /* ... JSX code ...*/
        }

        componentDidUpdate(prevProps, prevState, snapshot) {
            console.log('[Person.js] componentDidUpdate');

            // { message: 'From getSnapshotBeforeUpdate!'}
            console.log(snapshot);
        }
}
```
### 2.3 Component Unmounting
- **componentWillUnmount()**
    - used when a component is to be **removed from the DOM**

### 2.4 Component Errors
- static getDerivedStateFromError()
- componentDidCatch()

## 3 Functional Components – React Hooks
### 3.1 useState, userRef and useEffect
- **useState** is covered in the [first chapter (section 4)](1-base-features.md),
- **useRef** is covered in the [first chapter (section 8)](1-base-features.md),
- **useContext** is covered in the [fifth chapter (section 3)](5-context-api.md),
- **useEffect** React hook is a functional component alternative to **all important** class component lifecycle hooks.
    - **import** _useEffect_ from React,
    - it takes an **anonymous function** that will run for **every render cycle** (component creation or update),
    - posssible to use **multiple** _useEffect_ hooks in one component,
    - as if _componentDidMount_ and _componentDidUpdate_ in one effect,
    - **limiting execution**:
        - **component creation**: add a second argument to _useEffect_ – an empty array `[]`,
        - **data update**: add a second argument to _useEffect_ – an array containing all the pointers to data that you want to use your effect on data's change `[data1, data2, data3.property1]`,
        - **component cleanup**: provide another anonymous function as a return that will run as a cleanup based on the second argument of _useEffect_:
            - **no second argument**: for example canceling an operation of the component whenever it rerenders (updates),
            - **en empty array**: cleanup after the components unmouting (desctruction),
            - **array with pointers to data**: cleanup after pointed data change
```jsx
import React, { useEffect } from 'react';

const cockpit = props => {

// ... component code ...

// --- CREATION & UPDATE COMPONENT ---

    // On create component
    useEffect(() => {
        console.log('[Cockpit.js] useEffect 1');
        // Its own logic
    }, []);

    // On props.showPoeople change
    useEffect(() => {
        console.log('[Cockpit.js] useEffect 2');
        // For example http request ...
    }, [props.showPeople]);


// --- CLEANUP ---

    //Cleanup on component's every rerender
    useEffect(() => {
        console.log('[Cockpit.js] useEffect component every rerender');

        return () => {
           console.log('useEffect component cleanup with every rerender'); 
        }
    });

    //Cleanup on component destruction
    useEffect(() => {
        console.log('[Cockpit.js] useEffect component destruction cleanup');

        return () => {
           console.log('useEffect component destruction cleanup'); 
        }
    }, []);

    //Cleanup on pointed data's chnage
    useEffect(() => {
        console.log('[Cockpit.js] useEffect component destruction cleanup');

        return () => {
           console.log('useEffect component clean on pointed data change'); 
        }
    }, [props.showPeople, props.people]);

    return(
        <div className="Cockpit">
            <p className={classes.join(' ')}>Working dynamic styles!</p>
            <button 
            style={style}
            onClick={props.clicked}
            >Show People</button>
        </div>
    );
};
```
### 3.2 Performance Optimization
- Preventing **unnecessary rerendering**,
- possible with **memoization** – React stores snapshot of the component, only if the input changes, it will rerender it; if the inputs do not change and the parent component wants to update the component, React provides only the **snapshot** of the component and does not render it,
- **wrap** your component's export into React's memoization function `export default React.memo(cockpit)`

