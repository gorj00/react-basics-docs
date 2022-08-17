# VI. Axios – HTTP Requests
_...return to React Docs [chapters](./readme.md)_

## 1  Axios
- Package for creating http requests,
- more detailed [documentation](https://github.com/axios/axios),
- install the package with `npm install --save axios`

## 2 HTTP Requests
### 2.1 Class Components
- **componentDidMount()** lifecycle hook is suitable for side effect as http requests but it triggeres _rerender_,
- the axios requests are asynchronous; therefore, use promises or async/await JS feature or observables

```jsx
import axios from 'axios';

// ... inside class component

constructor(props) {
    super(props);

    this.apiUrl = 'http://api.example.com';
    this.state = {
        posts: [],
        newPost: [
            title: 'New title',
            body: 'New content', 
            author: 'Shakespear'
        ]
    };
}

componentDidMount() {
    // GET request
    axios.get(`${apiUrl}/posts`)
         .then(response => {
             this.setState({posts: response.data});
         });
}

postDataHandler = () => {
    const newPost = {
        title:  this.state.newPost.title,
        body:   this.state.newPost.body,
        author: this.state.newPost.author,
    }

    // POST request
    axios.post(`${apiUrl}/posts`, newPost)
         .then(response => {
             console.log(response.status);
         });
}

deleteDataHandler = () => {
    const postId = 2;

    // DELETE request
    axios.delete(`${apiUrl}/posts/${postId}`, newPost)
         .then(response => {
             console.log(response.status);
         });
}

```

## 3 Error Handling
Use **catch** block on promise. 

```jsx
import axios from 'axios';

// ... inside class component

constructor(props) {
    super(props);

    this.apiUrl = 'http://api.example.com';
    this.state = {
        posts: []
    };
}

componentDidMount() {
    // http request with catch block
    axios.get(`${apiUrl}/posts`)
         .then(response => {
             this.setState({posts: response.data});
         })
         .catch(error => {
             console.log(error);
             this.setState({error: true});
         });
}
```

## 4 Interceptors
Execute axios code **globally** (when request gets send or when response is received).

For example hangling global errors, setting common request header, logging all response etc.

### 4.1 Setting Up Interceptors
- In a global file (for example _index.js_), **import** axios,
- access **interceptors** on _axios_ object and access **use** method on **request/response** object `axios.interceptos.request.use()`,
- this method takes provides **request config** object, it **always must be returned** in the function; oterwise, it will block requests,
- same with global errors

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './containers/App';
import * as serviceWorker from './serviceWorker';

import axios from 'axios';

axios.interceptos.request.use(
    requestConfig => {
    console.log(requestConfig);

    // Must return it
    return requestConfig;
    },
    error => {
        console.log(error);
        return Promise.reject(error);
    }
);

axios.interceptos.response.use(
    responseConfig => {
    console.log(responseConfig);

    // Must return it
    return responseConfig;
    },
    error => {
        console.log(error);
        return Promise.reject(error);
    }
);

ReactDOM.render(<App />, document.getElementById('root'));

serviceWorker.unregister();
```

## 5 Axios Default Global Configuration
- In a global file (for example _index.js_), **import** axios, you can define properties on _axios_ **defaults** property, for example **baseUrl** `axios.defaults.baseURL = 'http://some.base.url.com'`,
- the requests then do not have contain the entire url, only their **specific endpoint** `axios.delete('${/posts/}postId')`,
- you can access on defaults, for example:
    - `axios.defaults.headers.baseURL`,
    - `axios.defaults.headers.common`,
    - `axios.defaults.headers.post['Content-Type'] = 'application/json'`,

## 6 Axios Instances
Creating different instances of axios with different global setting (different baseURL and so on). 

### 6.1 Create Axios Instance 
- **Create** a new file that will have the definition of the axios instance (for example axios.js),
- **import** axios, 
- **create axios instance** with `axios.create()` method,
- it takes a JS object, it may contain _baseURL_ and so on,
- all _default global configuration_ is applicable to this instance,
- **export** this instance,
- **import** this instance to your file with requests


