
## MEAN STACK DEPLOYMENT TO UBUNTU IN AWS

### Step 1: Intall Nodejs

Update ubuntu and upgrade 


~~~
sudo apt update
sudo apt upgrade
~~~


Add certificates


~~~
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates

curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
~~~




Install NodeJS


~~~
sudo apt install -y nodejs
~~~


### Step 2: Install MongoDB

Run the following commands



~~~
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6

echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
~~~



#### Install MongoDB

~~~
sudo apt install -y mongodb
~~~

Start the server


~~~
sudo service mongodb start
~~~


Verify that the service is up and running

~~~
sudo systemctl status mongodb
~~~


#### Install npm – Node package manager.

~~~
sudo apt install -y npm
~~~



Install body-parser package

#### We need ‘body-parser’ package to help us process JSON files passed in requests to the server.


~~~
sudo npm install body-parser
~~~



#### Create a folder named ‘Books’


~~~
mkdir Books && cd Books
~~~


In the Books directory, Initialize npm project


~~~
npm init
~~~


#### Add a file to it named server.js


~~~
vi server.js
~~~


#### Copy and paste the web server code below into the server.js file.



~~~
var express = require('express');
var bodyParser = require('body-parser');
var app = express();
app.use(express.static(__dirname + '/public'));
app.use(bodyParser.json());
require('./apps/routes')(app);
app.set('port', 3300);
app.listen(app.get('port'), function() {
    console.log('Server up: http://localhost:' + app.get('port'));
});
~~~




![Screenshot from 2023-01-07 19-59-27](https://user-images.githubusercontent.com/66005935/211166396-6a6a3b82-bfa1-4850-96e0-1ee0c13343cb.png)



### STEP 3 : INSTALL EXPRESS AND SET UP ROUTES TO THE SERVER


Install mongoose 


~~~
sudo npm install express mongoose
~~~


#### In ‘Books’ folder, create a folder named apps

~~~
mkdir apps && cd apps
~~~


Create a file named routes.js



~~~
vi routes.js
~~~



Copy and paste the code below into routes.js



~~~
var Book = require('./models/book');
module.exports = function(app) {
  app.get('/book', function(req, res) {
    Book.find({}, function(err, result) {
      if ( err ) throw err;
      res.json(result);
    });
  }); 
  app.post('/book', function(req, res) {
    var book = new Book( {
      name:req.body.name,
      isbn:req.body.isbn,
      author:req.body.author,
      pages:req.body.pages
    });
    book.save(function(err, result) {
      if ( err ) throw err;
      res.json( {
        message:"Successfully added book",
        book:result
      });
    });
  });
  app.delete("/book/:isbn", function(req, res) {
    Book.findOneAndRemove(req.query, function(err, result) {
      if ( err ) throw err;
      res.json( {
        message: "Successfully deleted the book",
        book: result
      });
    });
  });
  var path = require('path');
  app.get('*', function(req, res) {
    res.sendfile(path.join(__dirname + '/public', 'index.html'));
  });
};
~~~



![Screenshot from 2023-01-07 20-09-27](https://user-images.githubusercontent.com/66005935/211166729-3669be07-a3f8-4b65-8b60-8c445a56b0c6.png)



#### In the ‘apps’ folder, create a folder named models


~~~
mkdir models && cd models
~~~



Create a file named book.js



~~~
vi book.js
~~~


Copy and paste the code below into ‘book.js’

~~~
var mongoose = require('mongoose');
var dbHost = 'mongodb://localhost:27017/test';
mongoose.connect(dbHost);
mongoose.connection;
mongoose.set('debug', true);
var bookSchema = mongoose.Schema( {
  name: String,
  isbn: {type: String, index: true},
  author: String,
  pages: Number
});
var Book = mongoose.model('Book', bookSchema);
module.exports = mongoose.model('Book', bookSchema);
~~~



### Step 4: Access the routes with AngularJS


Change the directory back to ‘Books’

cd ../..

Create a folder named public


~~~
mkdir public && cd public
~~~


Add a file named script.js

vi script.js


Copy and paste the Code below (controller configuration defined) into the script.js file.



~~~
var app = angular.module('myApp', []);
app.controller('myCtrl', function($scope, $http) {
  $http( {
    method: 'GET',
    url: '/book'
  }).then(function successCallback(response) {
    $scope.books = response.data;
  }, function errorCallback(response) {
    console.log('Error: ' + response);
  });
  $scope.del_book = function(book) {
    $http( {
      method: 'DELETE',
      url: '/book/:isbn',
      params: {'isbn': book.isbn}
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
  $scope.add_book = function() {
    var body = '{ "name": "' + $scope.Name + 
    '", "isbn": "' + $scope.Isbn +
    '", "author": "' + $scope.Author + 
    '", "pages": "' + $scope.Pages + '" }';
    $http({
      method: 'POST',
      url: '/book',
      data: body
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
});
~~~





![Screenshot from 2023-01-07 20-25-18](https://user-images.githubusercontent.com/66005935/211167325-147e39ce-c749-4290-a8a2-184748159116.png)



In public folder, create a file named index.html;

vi index.html


Cpoy and paste the code below into index.html file.




![Screenshot from 2023-01-07 20-28-44](https://user-images.githubusercontent.com/66005935/211167406-6e35fb99-6fc0-4ed2-947a-71be4e9cc37b.png)



Change the directory back up to Books

cd ..

Start the server by running this command:


~~~
node server.js
~~~


The server is now up and running, we can connect it via port 3300. You can launch a separate Putty or SSH console to test what curl command returns locally.

curl -s http://localhost:3300


It shall return an HTML page, it is hardly readable in the CLI, but we can also try and access it from the Internet.

For this – you need to open TCP port 3300 in your AWS Web Console for your EC2 Instance.





![Screenshot from 2023-01-07 20-36-53](https://user-images.githubusercontent.com/66005935/211167625-c6c48685-78cc-4226-a5cb-e456db205643.png)










































