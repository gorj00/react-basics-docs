# II. List and Conditionals
_...return to React Docs [chapters](./readme.md)_

- To use Javascript expressions in the component's **return()**, place your expressions between curly braces `{ JS expression }`,
- it does not allow complex blocks, only simple statements.

## 1 Rendering Conditional Content
### 1.1 Ternary Expression
- Use **ternary expression** `exp ? true case : false case` as an alternative to _if () else ()_ blocks, 
- example: if the state's property, which we get to toggle, is _true_, show people code, if _false_, do not render anything:
```jsx
return ( 
    <div className="component-style"> 
        <button onClick={toggleContentHandler}>
            Show Content
        </button>
        {
          currentState.showContent ? 
            <div>
            <Person 
                name={currentState.persons[0].name} 
                age={currentState.persons[0].age} 
            />
            <Person 
                name={currentState.persons[1].name} 
                age={currentState.persons[1].age}
            />
          </div> : null
      }
      </header>
    </div>
  );
```

### 1.2 Conditional Logic Outside of return()
- An alternative is to handle the conditional logic outside of JSX return code,
- preferred way to ternary operator method,
- if class component, handle the logic outside of return() and inside **render()** method,
- **example** of functional component: 
```jsx
  // Handling conditional logic for people
  let people = null;
  if (peopleState.showPeople) {
    people = (    
      // JSX expression
      <div>
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

  return ( 
    <div className="component-style"> 
        <button onClick={togglePersonHandler}>
            Show People
        </button>
        {people}
    </div>
  );
```

## 2 Outputting Lists
- Outputting **arrays** dynamically is possible with Javascript's **map()** function,
- React expects every list item to have a unique **key** identifier,
- **example**: 
```jsx
<div>
    {peopleState.persons.map(person => {
        return (
            <Person
                name={person.name}
                age={person.age}
                key={person.id}
            />
        );
    })}
</div>
```

- When changing the array (updating, deleting), it is important to respect the rule **immutability** of the state in reference to _reference types_,
- recommended approach is to always create a copy of the array and then only edit the copy itself.

**Example**:
```jsx
// parent functional component

  const [peopleState, setPeopleState] = useState(initialState);

  const deletePersonHandler = personIndex => {
    // Reference type! cannot change the current state with splice()
    // const people = peopleState.persons;
    const people = [...peopleState.persons];
    // removing that person
    people.splice(personIndex, 1);

    // updating the state
    setPeopleState({
      ...peopleState,
      persons: people
    });
  }

  return ( 
    <div className="parent-component-style"> 
        <button onClick={togglePersonHandler}>
            Show People
        </button>
        {peopleState.persons.map((person, index) => {
          return (
            <Person
              name={person.name}
              age={person.age}
              key={person.id}
              change={event => nameChangeHandler(event, person.id)}
              clickRef={() => deletePersonHandler(index)}
            />
          );
        })}
    </div>
  );
```
```jsx
// child functional component 

const person = (props) => {

    return (
        <div className="Person">
            <p onClick={props.clickRef}>I'm {props.name} and I am {props.age} years old!</p>
            <p>{props.children}</p>
            <input type="text" onChange={props.change} value={props.name} />
        </div>
    )
};
```



