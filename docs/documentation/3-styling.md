# III. Styling
_...return to React Docs [chapters](./readme.md)_

## 1 Basic Styling
### 1.1 Stylesheets
- Stylesheets are applied **globally**,
- it can be handled with a **component root class** and **prefixing** your styles in the stylesheet with root class,
- **import** the stylesheet file into your component `import './ComponentStylesheet.css';`

**Example**: 
```jsx
// Person.js

import React from 'react';
import './Person.css';

const person = (props) => {
    return (
        <div className="Person">
            <button className="button">Click me!</button>
           // ... code
        </div>
    )
};

export default person;
```
```css
/* Person.css */

/* root class */
.Person {
    width: 60%;
    margin: 16px auto;
    border: 1px solid #eee;
    box-shadow: 0 2px 3px #ccc;
    padding: 16px;
    text-align: center;
}

/* button style class with a root class prefix selector */
.Person .button {
    background: blue;
}
```

### 1.2 Inline Styles
- On a JSX element, use **style** property that accepts Javascript object of CSS properties and values,
- the object property must be either in **camelCase** `backgroundColor` or in **quotation marks** `'background-color'`; all non-numerical values must be in quotation marks `'8px'`, `'red'` etc.,
- **scoped** only to the component/element it is added to,
- **does not** allow **pseudo selectors** (_hover_ atc.)

**Example:** 
```jsx
// Person.js

import React from 'react';
import './Person.css';

const person = (props) => {
    const style = {
        backgroundColor: 'white',
        font: 'inherit',
        border: '1px solid blue',
        padding: '8px'
    };

    return (
        <div className="Person">
            <button className="button">Click me!</button>
           // ... code
        </div>
    )
};

export default person;
```

## 2 Dynamic Styles
### 2.1 Inline Styles
- Same as dynamic content, see [previous chapter]('./2-lists-and-conditionals.md'),
- **example**:
```jsx
  const style = {
    backgroundColor: 'green',
  };

  // Dynamic rendering
  let people = null;
  if (peopleState.showPeople) {
    people = (
      <div>
        {peopleState.persons.map((person, index) => {
          return (
            <Person
              name={person.name}
              age={person.age}
              change={event => nameChangeHandler(event, person.id)}
              clickRef={() => deletePersonHandler(index)}
              key={person.id}
            />
          );
        })}
      </div>
    );

    // Dynamic change of styles
    style.backgroundColor = 'red';
  }

  return ( 
    <div className="component-style"> 
        <button 
            style={style}
            onClick={togglePersonHandler}
        >Show People</button>
        {people}
    </div>
  );

  // Based on toggle boolean value, button changes colors
```
### 2.2 Class Names

**Creating a multiple classes list**:
- define an array of dynamic **class names** of classes defined in the stylesheet,
- apply **join** method on them with an **empty space**,
- example: `const classes = [...dynamicClassNames].join(' ')`
- it will result in `'className1 className2 className3'`,
- assign this _classes_ to the **className** property of the component/element `<button className={classes}>Click me</button>`,
- **another example**: 
```jsx
const classes = [];
if (peopleState.persons.length <= 2) {
    classes.push('red'); // classes = ['red']
}
if (peopleState.persons.length <= 1) {
    classes.push('bold'); // classes = ['bold']
}

  return ( 
    <div className="component-style"> 
        <p style={classes.join(' ')}>
            Paragraph styled based on the number of people showed
        </p>
    </div>
  );
```

## 3 Using Radium – _pseudo selectors_ and _media queries_ in Inline Styles
**Radium** allows to use **pseudo selectors** and **media queries** in **inline styles**.

- **Install** _radium_ with `npm install --save radium`,
- **import** _radium_ and _StyleRoot_ in your component file `import Radium, { StyleRoot } from 'radium'`,
- **wrap** your component _export_ with Radium higher level component `export default Radium(ComponentName)`,
- **wrap** your **entire app**'s JSX return in _StyleRoot_ element (necessary for _media queries_, possible to omit for _pseudo elements_):
```jsx
// app.js

// ... component code ...

  return ( 
    <StyleRoot>
      <div className="App"> 
        // ... JSX code ...
      </div>
    </StyleRoot>
  );

```

### 3.1 Using _pseudo selectors_ (:hover, :visisted etc.)
- In the object, where defining the inline styles, **create** a **pseudo selector** property in **quatation marks** and **assign** it its own object of styles,
**hover** style example:
```jsx
  const style = {
    backgroundColor: 'white',
    color: 'grey',
    ':hover': {
      backgroundColor: 'lightgreen',
      color: 'black'
    }
  };
```
- when changing the pseudo selector styles, for example in conditional rendering, the property can be **accessed** with **square brackets** (not with _dot_), example:
```jsx
    // Inside conditional rendering

    // Dot notation
    style.backgroundColor = 'red';

    // Square brackets notation
    style[':hover'] = {
      backgroundColor: 'salmon',
      color: 'black'
    }
```

### 3.2 Using _media queries_
- In the object, where defining the inline styles, **create** a **media queries selector** property in **quatation marks** and **assign** it its own object of styles,
- **example**:
```jsx
  const style = {
    backgroundColor: 'white',
    color: 'grey',
    '@media (min-width: 500px)': {
        width: '450px'
    }
  };
```

## 4 CSS Modules
CSS modules allow to **scope** the styles only to the component (styles are not global).

- Requires _react-scripts@2.0.0+_,
- create a CSS module stylesheet file `moduleName.module.css`,
- in the component file, **import** the CSS module as **styles** `import styles from './moduleName.module.css'`,
- in the component file, **access** the class name on the _styles_ object with dot notation `<Element className={styles.className1} />` or with square brackets `<Element className={styles['class-name-1']} />`,
- **nested classes** are available too, `button.danger { ... }` can be accessed as `className={styles.danger}` on a _button_ element,
- the CSS module's class name results in **[filename]\_[classname]\_\_[hash]**,
- **example**: 

```jsx
// Person.css - applied globally
.text-colored {
    color: blue;
}
```
```jsx
// Person.module.css - scoped to Person component only
.text-colored {
    color: red;
}

```
```jsx
// Person.js
import './Person.css';
import styles from './Person.module.css';

const person = (props) => {

    return (
        <div className="Person">
            <p className={styles['text-colored']}>I'm {props.name} and I am {props.age} years old!</p>
        </div>
    )
};
```
- results in `<p class="Person_error__11mkS">` => **red** text
  - the style from CSS module is applied, 
  - the style from the global stylesheet is not
