# V. Context API - Sharing Data Across Nested Components
_...return to React Docs [chapters](./readme.md)_

## 1  Sharing Data in Nested Components
To prevent creating a deep **props chain** when sharing props across multiple nested components, use **Context API**; it is possible to use _Redux_ as an alternative.

## 2 Create Context
- Create context (ideally _context folder_) in a context file with `React.createContext()` method

```jsx
// context/feature-context.js

    import React from 'react';

    const featureContext = React.createContext(
        // example of value initialization
        // {
        //     authenticated: false,
        //     peopleCount: 5
        // }
    );

    export default featureContext;
```

### 2.1 Providing values
- it is used as a **component with Provider property** `<ContextComponent.Provider>` that wraps all the components that need to have access to this data, Provider property then has its own **property**:
    - **value**: if not specified, value can be **initialized** in the context file in the _createContext_ method as an argument,
    - **warning**: changing values in context provider **does not rerender** the component; therefore, it respects that React rerenders only on a _props_ or _state_ change - you have to provide the values with _props_ or _state_
```jsx
// ParentComponent.js - providing context

    import FeatureContext from 'context/feature-context';
    // ... other imports

    // ... component code

    return ( 
        <Aux> 
            <FeatureContext.Provider 
                value={{
                    authenticated: this.state.authenticated,
                    login: this.loginHandler
                }}
            >
                <Component1 />
                <Component2 />
            <FeatureContext.Provider>
        </Aux>
    );
```

### 2.2 Consuming values in JSX
- it is used as a **component with Consumer property** `<ContextComponent.Consumer>`,
- it doesn't take the wrapped JSX code as children props property, it takes it as an **function** with **context parameter**
```jsx
// ParentComponent.js - providing context

    import FeatureContext from 'context/feature-context';
    // ... other imports

    // ... component code

    return (
        <Aux>
            <FeatureContext.Consumer>
                {context => context.authenticated ? <p>Authenticated!</p> : <p>Please, login!</p>}
            </FeatureContext.Consumer>

            <p>{props.children}</p>
        </Aux>
    )
```

### 2.3 Example with Context API
**Example** with mock authentication, context value accessible in _cockpit_ and _people_ (and therefore _person_ as well): 
```jsx
// context/auth-context.js

    import React from 'react';

    const authContext = React.createContext();

    export default authContext;
```
```jsx
// App.js - providing context

    import AuthContext from 'context/auth-context';
    // ... other imports

    // ... component code

      // Handling conditional logic for people
    let people = null;
    if (peopleState.showPeople) {
        people = (
        // JSX expression
        <div>
            <People 
            persons={peopleState.persons}
            change={nameChangeHandler}
            clickRef={deletePersonHandler}
            />
        </div>
        );
    }

    return ( 
        <div className="App"> 
        <header className="App-header">
            <button
                onClick={() => {
                    this.setState({showCockpit: false});
                }}
            >
                Remove Cockpit
            </button>
                <AuthContext.Provider 
                    value={{
                        authenticated: this.state.authenticated,
                        login: this.loginHandler
                    }}
                >
                {this.state.showCockpit ? (
                    <Cockpit 
                    showPeople={peopleState.showPeople}
                    peopleLength={peopleState.persons.length}
                    clicked={togglePersonHandler}
                    />
                ) : null}
                {people}
            <AuthContext.Provider>
        </header>
        </div>
    );
```
```jsx
// Cockpit.js - consuming context
    import AuthContext from 'context/auth-context';
    // ... other imports

    // ... component code

    return(
        <div className="Cockpit">
            <p className={classes.join(' ')}>Working dynamic styles!</p>
            <button 
                style={style}
                onClick={props.clicked}
            >Show People</button>

            <AuthContext.Consumer>
                {context => <button onClcik={context.login}>Login in</button>}
            </AuthContext.Consumer>

        </div>
    );

```
```jsx
// Person.js - consuming context
    import AuthContext from 'context/auth-context';
    // ... other imports

    // ... component code

    return (
        <Aux>
            <AuthContext.Consumer>
                {context => context.authenticated ? <p>Authenticated!</p> : <p>Please, login!</p>}
            </AuthContext.Consumer>
            {this}
            <p onClick={props.clickRef} className={styles['text-colored']}>I'm {props.name} and I am {props.age} years old!</p>
            <p>{props.children}</p>
            <input type="text" onChange={props.change} value={props.name} />
        </Aux>
    )

```
## 3 Consuming Context Values in Component Logic (and JSX)
### 3.1 Class Components
- Add a static property **contextType**,
- **assign** it the imported context obbject,
- **access** the context object on **this.context** property of the class component:

```jsx
import React, { Component} from 'react';
import FeatureContext from 'context/feature-context';

class Person extends Component {
    static contextType = FeatureContext;

    componentDidMount() {
        console.lgo(this.context.authenticated);
    }

    // ... component JSX
}
```

### 3.2 Functional Components
- Use **useContext()** hook and store it in a constant

```jsx
import React, { useContext } from 'react';
import FeatureContext from 'context/feature-context';

const cockpit = props => {
    const featureContext = useContext(FeatureContext);

    console.log(featureContext.authenticated);
    
    // ... component JSX
}
```

