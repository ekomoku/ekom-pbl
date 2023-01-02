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










