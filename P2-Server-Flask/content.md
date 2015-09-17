---
title: "Building a Server with Flask"
slug: server-flask
---

#Lecture

<iframe src="//www.slideshare.net/slideshow/embed_code/key/6VtuKX16sLj0GB" width="100%" height="400" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe>

#Getting Started

Now that you have a basic understanding of how we will build the Trip Planner backend using flask, let's get started with the setup.

##Python 3

First of all, ensure that python 3 is currently installed by running this line in the terminal:

	python3 --version
	
If you have python 3 installed, you should see a response that confirms the latest python version (it should be 3.4 or higher):

	Python 3.4.0
	
If python 3 is not installed you should install it via homebrew:

	 brew update
	 brew install python3
	 
##Installing & Running MongoDB

Another prerequisite besides python 3 is MongoDB. **Whenever the flask server is running, you need to run a MongoDB instance as well.** Otherwise the server won't be able to access the DB and will throw an exception. 

You can test if MongoDB is installed by starting an instance of the DB with the following terminal command:

	mongod
	
If the DB starts up you can move on to the next step! Keep `mongod` running in the current terminal tab and open a new tab (CMD + T) in which you'll enter the terminal commands of the following steps.

If the command isn't recognized, you need to install MongoDB via homebrew:

	brew update
	brew install mongodb
	
Once the install completes you need to start the DB with this command:

	mongod
	 
##Starter Project

We will be using a bunch of libraries to speed up development. While you can configure your project from scratch, I'd recommend starting with the starter project that has all of the dependencies defined and comes with a basic skeleton for your first web service.

Start by [downloading the starter project](https://github.com/MakeSchool/Flask-Starter-Project/archive/master.zip). Unzip the folder and navigate to the root folder of the project.

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

	python tests.py
	
	> Ran 2 tests in 0.023s
	>
	> OK
	
This indicates that all tests passed successfully! Now we can dive into discussing the code that is provided with the starter project - after that you'll be ready to get started with developing your own web service.


##The Starter Project Code

Now we'll dive into the code that the starter project provides. Discussing the code should give you a lot of insight into writing RESTful webservices with flask. We'll start by discussing the `server.py` file.

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

