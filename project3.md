## Project 3: MERN Stack Implementation

### Step 1: Backend Configuration

Run the following commands to update ubuntu

~~~
sudo apt update
sudo apt upgrade
~~~


To get the location of Node.js in Ubuntu repositories

~~~
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
~~~

#### Install Node.js

~~~
sudo apt-get install -y nodejs
~~~



![Screenshot from 2023-01-02 16-04-25](https://user-images.githubusercontent.com/66005935/210248884-04385004-90de-4053-a4e2-58f6564ef266.png)




#### Application Code Setup

Create new directory for the To-Do Project

~~~
mkdir Todo
~~~


Change directory to the project folder( cd Todo) and initialize the project

~~~
npm init
~~~



![Screenshot from 2023-01-02 16-52-35](https://user-images.githubusercontent.com/66005935/210254215-01c23b8b-1a02-4543-b37b-6c5559b8c416.png)



### Install ExpressJs

~~~
npm install express
touch index.js
npm install dotenv
~~~


Open the index.js file and paste the config shown


~~~
vim index.js
~~~



![Screenshot from 2023-01-02 17-11-59](https://user-images.githubusercontent.com/66005935/210256439-7de23cc0-8ee4-464f-a758-a474ab877d1d.png)




Start the server by running the command


~~~
node index.js
~~~


![Screenshot from 2023-01-02 17-10-56](https://user-images.githubusercontent.com/66005935/210256466-6a0bf73f-68ba-487e-9986-aaca43829b90.png)


Showing on the browser using server's public address on port 5000 (http://18.212.11.59:5000)



![Screenshot from 2023-01-02 17-28-41](https://user-images.githubusercontent.com/66005935/210258128-6ea0a226-43ca-4489-9be5-84ac4fabdacd.png)



### Routes

Creating routes that that will define various endpoints that the To-do app will depend on. Also create api.js in the 'routes' folder

~~~
mkdir routes
touch api.js
routes > vim api.js
~~~



![Screenshot from 2023-01-02 17-44-41](https://user-images.githubusercontent.com/66005935/210259144-5bc94909-0bbb-4e3a-83d3-0735a1b64e98.png)



### Creating Models

First install mongoose in the 'Todo' folder and create a folder 'models'. Also create a file 'todo.js


~~~
npm install mongoose
mkdir models
touch todo.js
vim todo.js
~~~



![Screenshot from 2023-01-02 17-58-38](https://user-images.githubusercontent.com/66005935/210260513-443eede3-bff5-464a-859b-196f91def344.png)



![Screenshot from 2023-01-02 18-13-25](https://user-images.githubusercontent.com/66005935/210261959-9a2a0a01-a0e2-423a-9e52-ea0d4a103a94.png)



### Mongodb Database


We'll use mLab for mongodb as a service (DBaaS). Create an account and create a database.

Copy the link under 'Connect > connect your application' . Create a .env file in Todo folder and paste the link ( chnage the <password> to the actual password used)

Next, update the index.js to reflect the use of .env so that Node.js can connect to the database. Paste the config as shown in the image

~~~
vim index.js
~~~



![Screenshot from 2023-01-02 20-58-23](https://user-images.githubusercontent.com/66005935/210273912-b0127178-f757-49dc-acdd-d98743122831.png)


Start the server and confirm the backend is connected
 

~~~
node index.js
~~~

  
  
![Screenshot from 2023-01-02 21-10-16](https://user-images.githubusercontent.com/66005935/210274822-b34701d1-52cb-4f38-baea-e6183f3594e0.png)

  

### Testing Backend Code without Frontend using RESTful API
 
  
 
On POSTMAN, create a POST request to the API http://54.175.233.216:5000/api/todos. This request sends a new task to our To-Do list so the application could store it in the database.

Note: make sure your set header key Content-Type as application/json
 
POST Request
 

 ![Screenshot from 2023-01-05 22-06-08](https://user-images.githubusercontent.com/66005935/210879966-a369246e-d149-4cd6-8996-426ba012855d.png)

 
GET Request
 

 
![Screenshot from 2023-01-05 22-09-31](https://user-images.githubusercontent.com/66005935/210880587-55069807-d37d-4efa-99ef-19feca2dd34b.png)


 
### Frontend Creation
 
In Todo directory, Run the following command to create react app. It will create folder called client, where all the reat codes will be 


~~~
 npx create-react-app client
~~~
 

Install concurrently and nodemon in Todo directory using the following codes

 
~~~
npm install concurrently --save-dev
npm install nodemon --save-dev
~~~
 
 
 
In Todo folder open the package.json file. Change the highlighted part of the below screenshot and replace with the code below.

 
~~~
"scripts": {
"start": "node index.js",
"start-watch": "nodemon index.js",
"dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
},
~~~
 
 
![Screenshot from 2023-01-05 22-41-57](https://user-images.githubusercontent.com/66005935/210885478-1f016d86-1eb7-45f3-b4e0-b71444868919.png)

 
#### Configure Proxy in package.json

 
 Change directory to client and open package.json
 
 
~~~
cd client
vi package.json
~~~
 
 
On the first line of the package.json file, Add the key value pair in the package.json file "proxy": "http://localhost:5000".
The whole purpose of adding the proxy configuration in above is to make it possible to access the application directly from the browser by simply calling the server url like http://localhost:5000 rather than always including the entire path like http://localhost:5000/api/todos
 
#### Run the app with the foloowing command, make sure you're in the Todo folder
 

~~~
npm run dev
~~~
 
 Open browser http://<public-IP address>:3000
 
 
 
 ![Screenshot from 2023-01-07 17-14-48](https://user-images.githubusercontent.com/66005935/211160214-4f663d18-1703-43de-bcbd-c5824cb1a840.png)

 
 

 ![Screenshot from 2023-01-05 22-52-56](https://user-images.githubusercontent.com/66005935/210887254-20a6e77d-dcb9-446f-93fb-e824d87f2d67.png)

 Open port 3000 on EC2 to allow the app run on the browser
 
 
 ### Creating your React Components
 
 From your Todo directory run
 
 
~~~
 cd client
~~~
 
 
move to the src directory

 
~~~
cd src
~~~
 
 
Inside your src folder create another folder called components

 
 
~~~
mkdir components
~~~
 
 
 
Move into the components directory with

 
~~~
cd components
~~~
 
 
Inside ‘components’ directory create three files Input.js, ListTodo.js and Todo.js.

 
~~~
touch Input.js ListTodo.js Todo.js
Open Input.js file
vi Input.js
~~~
 
 
#### Copy and paste the following


 
~~~
import React, { Component } from 'react';
import axios from 'axios';

class Input extends Component {

state = {
action: ""
}

addTodo = () => {
const task = {action: this.state.action}

    if(task.action && task.action.length > 0){
      axios.post('/api/todos', task)
        .then(res => {
          if(res.data){
            this.props.getTodos();
            this.setState({action: ""})
          }
        })
        .catch(err => console.log(err))
    }else {
      console.log('input field required')
    }

}

handleChange = (e) => {
this.setState({
action: e.target.value
})
}

render() {
let { action } = this.state;
return (
<div>
<input type="text" onChange={this.handleChange} value={action} />
<button onClick={this.addTodo}>add todo</button>
</div>
)
}
}

export default Input
~~~
 
 
 
 
#### To make use of Axios, which is a Promise based HTTP client for the browser and node.js, you need to cd into your client from your terminal and run yarn add axios or npm install axios.

Move to the src folder

cd ..
Move to clients folder

cd ..
Install Axios

 
 ~~~
 npm install axios
 ~~~
 
 
 Go to ‘components’ directory

 
~~~
cd src/components
~~~
 
 
After that open your ListTodo.js

~~~
vi ListTodo.js
~~~
 
 
#### in the ListTodo.js copy and paste the following code

 
~~~
import React from 'react';

const ListTodo = ({ todos, deleteTodo }) => {

return (
<ul>
{
todos &&
todos.length > 0 ?
(
todos.map(todo => {
return (
<li key={todo._id} onClick={() => deleteTodo(todo._id)}>{todo.action}</li>
)
})
)
:
(
<li>No todo(s) left</li>
)
}
</ul>
)
}

export default ListTodo
~~~
 
 
 
#### Then in your Todo.js file you write the following code


 
~~~
import React, {Component} from 'react';
import axios from 'axios';

import Input from './Input';
import ListTodo from './ListTodo';

class Todo extends Component {

state = {
todos: []
}

componentDidMount(){
this.getTodos();
}

getTodos = () => {
axios.get('/api/todos')
.then(res => {
if(res.data){
this.setState({
todos: res.data
})
}
})
.catch(err => console.log(err))
}

deleteTodo = (id) => {

    axios.delete(`/api/todos/${id}`)
      .then(res => {
        if(res.data){
          this.getTodos()
        }
      })
      .catch(err => console.log(err))

}

render() {
let { todos } = this.state;

    return(
      <div>
        <h1>My Todo(s)</h1>
        <Input getTodos={this.getTodos}/>
        <ListTodo todos={todos} deleteTodo={this.deleteTodo}/>
      </div>
    )

}
}

export default Todo;
~~~
 
 

Move to the src folder

cd ..
Make sure that you are in the src folder and run

vi App.js
Copy and paste the code below into it
 
 
~~~
import React from 'react';

import Todo from './components/Todo';
import './App.css';

const App = () => {
return (
<div className="App">
<Todo />
</div>
);
}

export default App;
~~~
 
 
In the src directory open the App.css

vi App.css
Then paste the following code into App.css:
 
 
~~~
.App {
text-align: center;
font-size: calc(10px + 2vmin);
width: 60%;
margin-left: auto;
margin-right: auto;
}

input {
height: 40px;
width: 50%;
border: none;
border-bottom: 2px #101113 solid;
background: none;
font-size: 1.5rem;
color: #787a80;
}

input:focus {
outline: none;
}

button {
width: 25%;
height: 45px;
border: none;
margin-left: 10px;
font-size: 25px;
background: #101113;
border-radius: 5px;
color: #787a80;
cursor: pointer;
}

button:focus {
outline: none;
}

ul {
list-style: none;
text-align: left;
padding: 15px;
background: #171a1f;
border-radius: 5px;
}

li {
padding: 15px;
font-size: 1.5rem;
margin-bottom: 15px;
background: #282c34;
border-radius: 5px;
overflow-wrap: break-word;
cursor: pointer;
}

@media only screen and (min-width: 300px) {
.App {
width: 80%;
}

input {
width: 100%
}

button {
width: 100%;
margin-top: 15px;
margin-left: 0;
}
}

@media only screen and (min-width: 640px) {
.App {
width: 60%;
}

input {
width: 50%;
}

button {
width: 30%;
margin-left: 10px;
margin-top: 0;
}
}
~~~
 
 

In the src directory open the index.css

vim index.css
Copy and paste the code below:
 
 
~~~
body {
margin: 0;
padding: 0;
font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
"Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
sans-serif;
-webkit-font-smoothing: antialiased;
-moz-osx-font-smoothing: grayscale;
box-sizing: border-box;
background-color: #282c34;
color: #787a80;
}

code {
font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
monospace;
}
~~~
 

Go to the Todo directory

cd ../..
When you are in the Todo directory run:

npm run dev
 

 
 ![Screenshot from 2023-01-07 17-39-32](https://user-images.githubusercontent.com/66005935/211161546-08a62c71-1c2e-4dcf-9297-969380d7001c.png)

 

 
 

  
  
  
  
  
  
  
  
