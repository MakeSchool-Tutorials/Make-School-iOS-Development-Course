---
title: "Building a Server with Flask"
slug: server-flask
---

#Lecture

<iframe src="//www.slideshare.net/slideshow/embed_code/key/6VtuKX16sLj0GB" width="100%" height="400" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe>

[Download the slides here.](https://s3.amazonaws.com/mgwu-misc/MS-17/Slides/ServerFlask.pdf)

#GitHub Classroom Assignment

[Start the assignment by using this link.](https://classroom.github.com/assignment-invitations/cc2a161b4fb90279ae2620d13f22431c)

#Getting Started

Now that you have a basic understanding of how we will build the Trip Planner backend using flask, let's get started with the setup.

##Python 3

First of all, ensure that python 3 is currently installed by running this line in the terminal:

	python3 --version
	
If you have python 3 installed, you should see a response that confirms the latest python version (it should be 3.5 or higher):

	Python 3.5.0
	
If python 3 is not installed you should install it via homebrew:

	 brew update
	 brew install python3
	 
If you run into an issue while executing `brew update` and you're using Max OS X El Capitan you should run the following command:

	sudo chown -R $(whoami):admin /usr/local
	
If you're still having trouble you should [check out this post](https://github.com/Homebrew/homebrew/blob/master/share/doc/homebrew/El_Capitan_and_Homebrew.md).
	 
##Installing & Running MongoDB

Another prerequisite besides python 3 is MongoDB. **Whenever the flask server is running, you need to run a MongoDB instance as well.** Otherwise the server won't be able to access the DB and will throw an exception. 

You can test if MongoDB is installed by starting an instance of the DB with the following terminal command:

	mongod
	
Upon successful start you should see the following message:

	[initandlisten] waiting for connections on port 27017
	
Now your database is running and waiting for connections! **Keep `mongod` running in the current terminal tab and open a new tab (CMD + T) in which you'll enter the terminal commands of the following steps.** This will keep the database running which is required for your flask server to work.

If the command isn't recognized, you need to install MongoDB via homebrew:

	brew update
	brew install mongodb
	
Once the install completes you need to start the DB with this command:

	mongod
	 
`mongod` may notice that you have not specified a database directory. By default it uses /data/db. Because this folder may be missing, mongod may output the following error when run: 
`Data directory /data/db not found., terminating` 

If this happens, create a database location for your user using the following command:

        sudo mkdir -p /data/db
        
then change the ownership of the file as following:

	sudo chown -R $USER /data/db
	
Now you should be able to run `mongod`. If you still run into issues try to consult this [Stack Overflow question](http://stackoverflow.com/questions/7948789/mongodb-mongod-complains-that-there-is-no-data-db-folder).
	 
##Starter Project

We will be using a bunch of libraries to speed up development. While you can configure your project from scratch, I'd recommend starting with the starter project that has all of the dependencies defined and comes with a basic skeleton for your first web service.

You should have access to the starter project through GitHub Classroom.

###Creating and Activating a Virtual Environment

When setting up a development environment developers need to install numerous different dependencies. Ideally the dependencies of one project on the system should not interfere with dependencies from other projects. Therefore most developers set up some sort of sandbox in which they configure our development environment. One such sandboxing mechanism for python is called [*virtualenv*](https://virtualenv.pypa.io/en/latest/). Virtualenv allows us to create an isolated environment in which we can install the `pip` dependencies for our project.

Create a new virtual environment, in the root folder of the starter project, with the following command:

	virtualenv -p python3 development
	
The `-p` flag lets us choose the default python version for this environment. We choose python 3. The last argument `development` is the name of the new environment we're creating.
	
Now you can activate the virtual environment with this command:

	source development/bin/activate
	
You should see that your bash session is prefixed with the name of the virtual environment, e.g.:

	(development)Benjamins-MacBook-Pro:Flask-Starter-Project-master benjaminencz$
	
You should test which version of python is used by default within this environment. If the setup worked correctly it should be python 3:

	python --version
	> Python 3.4.0
	
Now we've created and activated a virtual environment for our project. We're ready to install the dependencies.

###Installing Project Dependencies

There are two different ways to install dependencies with `pip`. You can provide the name of a dependency directly, or you can reference a file that stores the dependencies for a given project. For the starter project we provide a `requirements.txt` file that contains all the required dependencies for this project ([here you can read about `pip freeze`](https://pip.pypa.io/en/stable/reference/pip_freeze/)). You can install the dependencies with this command:

	pip3 install -r requirements.txt
	
Make sure you're using `pip3` since we're working with python 3. Now, with an instance of MongoDB running and all requirements installed, you should be able to run the tests for the server successfully:

	python3 tests.py
	
	> Ran 2 tests in 0.023s
	>
	> OK
	
This indicates that all tests passed successfully! Now we can dive into discussing the code that is provided with the starter project - after that you'll be ready to get started with developing your own web service.


##The Starter Project Code

Now we'll dive into the code that the starter project provides. Discussing the code should give you a lot of insight into writing RESTful webservices with flask. We'll start by discussing the `server.py` file.

###Basic Setup Code

Here's the basic setup code that's needed for almost any flask app:

	from flask import Flask, request, make_response
	from flask_restful import Resource, Api
	from pymongo import MongoClient
	from bson.objectid import ObjectId
	from utils.mongo_json_encoder import JSONEncoder
	
	# Basic Setup
	# 1
	app = Flask(__name__)
	# 2
	mongo = MongoClient('localhost', 27017)
	# 3
	app.db = mongo.develop_database
	# 4
	api = Api(app)
	
The first few lines are mostly boilerplate. First we import all the dependencies that we use throughout the rest of the file. Then we perform the following steps to set up the flask app:

1. We create a flask instance and assign it to the `app` variable
2. We establish a connection to our MongoDB service that's running locally
3. We specify a particular database (`develop_database`) which we'll use to store data. We assign it to `app.db`. Throughout the rest of `server.py` we'll access `app.db` whenever we need to communicate with the DB. 
4. We create an instance of the `flask_restful` API. Later we'll add different endpoints to that API. The `flask_restful` library is not necessary for creating RESTful APIs, but it makes our lives a little easier by providing a specific format for defining endpoints for the different resources in our app.

###Implementing a Resource

Below the setup code that we just discussed we're implementing our first resource:

	#Implement REST Resource

	class MyObject(Resource):
	
	    def post(self):
	      #1
	      new_myobject = request.json
	      #2
	      myobject_collection = app.db.myobjects
	      #3
	      result = myobject_collection.insert_one(new_myobject)
		  #4
	      myobject = myobject_collection.find_one({"_id": ObjectId(result.inserted_id)})
		  #5
	      return myobject
	
	    def get(self, myobject_id):
	      #6
	      myobject_collection = app.db.myobjects
	      #7
	      myobject = myobject_collection.find_one({"_id": ObjectId(myobject_id)})
	
		  #8
	      if myobject is None:
	        response = jsonify(data=[])
	        response.status_code = 404
	        return response
	      else:
	        return myobject
	    
	      
This small starter project only has a single resource (`MyObject`). Most apps will define one resource for each entity that can be stored in the app (e.g. `User`, `Post`, etc.). The resource implements one method for each HTTP verb that is supported. 

Let's take a look at the implementation in detail, starting with the `post` method. The `post` method is invoked to create a new instance of `MyObject` on the server. The client that calls this endpoint provides a JSON body as part of the HTTP request:

1. We access the JSON that the client provided through the `request.json` variable. The `request` variable is implicitly available through the [request context](http://flask.pocoo.org/docs/0.10/reqcontext/). 
2. We access the collection in which we will store the new object. Typically we create one collection per entitiy type (e.g. `User`, `Post`, etc.).
3. We insert the JSON document into the collection. MongoDB is schema free, this means we can store JSON of any arbitrary structure in this collection. In a more complex application you would might want to validate the JSON structure to a certain degree. For this application we will trust the client to provide the correctly structured information.
4. After inserting the document we retrieve the `result`. Then we use this result to fetch the inserted document from the collection using the `find_one` method. The `find_one` method takes a dictionary that describes the filter criteria for our documents (in this case documents with a specific id). The `_id` field is automatically maintained by MongoDB and stores the unique identifier for each document that is stored. Note that we need to wrap the `result.inserted_id` into an `ObjectId` type. The `ObjectId` type is not a string! If you try to compare it to a string you won't get any results.
5. We return the selected document to the client. Now the client will be able to retrieve the `_id` generated by MongoDB and will know which id is associated with the new document on the server.

The `get` method for `MyObject` is a little bit simpler:

6. We reference the `myobjects` collection from which we'll select the document that the client is trying to access.
7. We build a query based on the `myobject_id` that we have received as part of the client's request. Later you'll see how this argument is handed to the `get` method.
8. If we can't find a document with the provided id we return a 404 status code. If we found a document we return it to the client.

That's all the code that goes into implementing a very simple resource! For this application our server is just acting as a thin layer above the DB.

###Adding Routes

The next important aspect of the starter project code is the mapping between routes and resources. A route defines a URL that can be called by a client application. Our simple server only has one route, here's the code that defines it:

	api.add_resource(MyObject, '/myobject/','/myobject/<string:myobject_id>')
	
The first parameter is the resource which we want ot map to a specific URL. Next, we have a collection of different URLs that map to that resource. For this application there are two different ways to call the *myobject* endpoint. The first one is */myobject/*, without a specific object id. This endpoint is used to create new instances. The second endpoint takes an object id, e.g. */myobject/2*. This endpoint is used to retrieve a specific object. 

With this additional line of setup, our server will now know which server methods to call when a specific URL is requested by the client.

###Additional Configuration

We have two additional configuration blocks in this starter project that I would like to discuss briefly. The first one configures a custom JSON serializer for our flask app:

	# provide a custom JSON serializer for flask_restful
	@api.representation('application/json')
	def output_json(data, code, headers=None):
	    resp = make_response(JSONEncoder().encode(data), code)
	    resp.headers.extend(headers or {})
	    return resp

A JSON Encoder takes python objects and turns them into a JSON text representation. For this application we need a custom serializer, because the default serializer does not know how to handle MongoDB's `ObjectID`s. A little earlier we discussed that an `ObjectID` is a specific type that is used to refer to a document in a MongoDB instance. This `ObjectID` isn't a string, so it cannot be serialized by Flask's default serializer.

With the lines above we install our custom encoder for any response that has the MIME type *application/json* (Read more about custom encoders [here](http://flask.pocoo.org/snippets/119/)).

The code for our custom encoder lives in this file of the starter project: `/utils/mongo_json_encoder.py` - take a look at the implementation if you're curious about the implementation!

The last part of the source file is the standard boilerplate code for starting a flask server:

	if __name__ == '__main__':
	    # Turn this on in debug mode to get detailled information about request related exceptions: http://flask.pocoo.org/docs/0.10/config/
	    app.config['TRAP_BAD_REQUEST_ERRORS'] = True
	    app.run(debug=True)
	    
You can read more about the basic setup of flask server [here](http://flask.pocoo.org/docs/0.10/quickstart/). In addition to the basic setup we provide some configuration settings that will make debugging of this server easier.

This concludes the entire code for our server - as you can see getting started with flask is pretty straightforward!

Before it's your turn to create the Trip Planner backend, let's take a look at the tests that come with this starter project.

##Tests

We highly recommend that you use automated tests to verify that your backend code works as expected. This is much faster than using some sort of tool to issue HTTP requests and manually checking the result. 

To give you a good idea of how to start writing tests, we have provided a small testsuite along with this starter project. You can find it in `tests.py`.

We will take a look at the setup code and at one test case - that should give you a good idea of how to design your own tests.

###Setup code

The code within the `setUp` method runs before every individual test in your test suite runs. If you have 4 tests, this piece of code will be executed before each of them. 

The main use cases for a `setUp` method is preparing specific resources for a test and resetting state that might have been created by other tests. That way each tests runs on a clean slate.

Let's take a look at our `setUp` method:

	def setUp(self):
	      self.app = server.app.test_client()
	      # Run app in testing mode to retrieve exceptions and stack traces
	      server.app.config['TESTING'] = True
	
	      # Inject test database into application
	      mongo = MongoClient('localhost', 27017)
	      db = mongo.test_database
	      server.app.db = db
	
	      # Drop collection (significantly faster than dropping entire db)
	      db.drop_collection('myobjects')
	      
There are a few different steps going on here. In the very first line we get a reference to our server application. That reference allows us to change certain apects of the app, e.g. which database it uses.

In the next line we use that reference to change the configuration of the app to `'TESTING' = true`. This option means that we will see helpful debug information if our tests uncover issues when running the server.

In the next step we inject a different database into the application. When writing automated tests, it is very important to ensure that each tests runs in isolation. It shouldn't be influenced by any tests that ran prior to it and it shouldn't influence any tests that will run after it. For our app that means that we need to reset the DB after every test - since each test will create/delete entries in the DB.

We can only reset the DB if we have a reference to a DB connection. That's why we create a new DB connection in this `setUp` code and then tell the app to use that connection. 

Lastly we call `db.drop_collection('myobjects')` on our test database. Remember that this line will run before every test case. This ensures that any objects that have been created by earlier tests are removed. It is also possible to delete the entire DB, not only a specific collection. However, this operation is fairly slow. In general we want our tests to run as fast as possible, so that we can run them often without getting blocked.

When building tests for the Trip Planner app, remember that you need to drop all collections that you've been working with (e.g. trips, users, etc.).

###Test Case Code

We will discuss Unit Testing more extensively throughout this course, but by looking at one example test you will quickly be able to gather the most important aspects:

	def test_getting_object(self):
	      response = self.app.post('/myobject/', 
	        data=json.dumps(dict(
	          name="Another object"
	        )), 
	        content_type = 'application/json')
	
	      postResponseJSON = json.loads(response.data.decode())
	      postedObjectID = postResponseJSON["_id"]
	
	      response = self.app.get('/myobject/'+postedObjectID)
	      responseJSON = json.loads(response.data.decode())
	
	      self.assertEqual(response.status_code, 200)
	      assert 'Another object' in responseJSON["name"]
      
Almost all tests can (or should be able to be) broken down into three steps:

- **Arrange**: In this step you create the necessary environment for test, e.g. creating an object by calling the `post` endpoint.
- **Act**: In this step you trigger the code under test by performing another action, e.g. triggering a `get` request to retrieve an object
- **Assert**: In this last step you use assertions to verify that the result you retrieved is the expected result. In this case we expect the server to respond with a 200 status code (success) and we expect the object to have a name of *"Another Object"*

Thinking of these three steps should be a helpful guide for writing your own tests. You can read [this post](http://flask.pocoo.org/docs/0.10/testing/) to learn more about the basics of writing tests for flask applications.

##Now it's your turn!

With this basic setup working it's now your turn to extend this server to support all features that we need for our Trip Planner project:

- API for creating a trip with waypoints
- API for updating a trip with waypoints
- API for deleting a trip with waypoints
- API for retrieving a specific trip via its ID
- API for retrieving all trips for a specific user

You should define this API with two main resources: 

- `yourserver.com/trips`
- `yourserver.com/users` 

On these resources you should use the appropriate HTTP verbs (`GET`, `POST`, `PUT`, `DELETE`) to implement creating/updating/deleting trips and signing up / verifying users.

In the Trip Planner app all trips should be user specific. This means that a user can only see/modify trips that they created themselves. Therefor we will need to implement an authorization and authentication system for our backend.

We haven't discussed that aspect yet; so feel free to try implementing it, but don't worry if you feel lost.

In the next lecture we will discuss these security topics and you will learn what's necessary for implementing user based authenication for your webservices.
