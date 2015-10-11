---
title: "Authentication and Authorization with Flask"
slug: flask-auth
---

#Lecture

<iframe src="//www.slideshare.net/slideshow/embed_code/key/6VtuKX16sLj0GB" width="100%" height="400" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe>

[Download the slides here.](https://s3.amazonaws.com/mgwu-misc/MS-17/Slides/ServerAuth.pdf)

#Now it's your turn!

With the lessons learned from the lecture above you should extend your Trip Planner backend. Extend the backend to support the following features:

- Provide an endpoint that allows a client to register a new user by providing username and password
- Provide an endpoint that allows a user to verify credentials (username and password). It should return a status code of 200 (OK) for correct credentials and status code 401 (Unauthorized) for incorrect credentials
- The endpoint that allows clients to create trips should require authentication. The backend should associate the created trip with the authenticated user
- The endpoint that allows clients to retrieve trips should require authentication. It should only return trips that belong to the authenticated user

Remember that it's preferred that you write tests for these new features **before** you write the implementation.

#Snippets and Additional Resources

##Basic Auth in Flask

This [brief tutorial](http://flask.pocoo.org/snippets/8/) describes how to implement Basic Authentication with flask. It uses a very simple example for the authentication method. Instead of using a hardcoded username and password you should compare the provided credentials with the ones that you have stored in your database.

##Using the Bcrypt library

As encryption library you should be using `bcrypt` ([documentation](https://pypi.python.org/pypi/bcrypt/1.1.0)). Like any other python library you can install it through `pip3`. 

###Generating an Encrypted Password

To encrypt a password, provide the unencrypted password and a random salt:

	hashed = bcrypt.hashpw(userPassword, bcrypt.gensalt(12))
	
The number provided as part of `gensalt` is called the *work factor*. The higher the work factor, the longer it takes to generate a hash. This also means that attackers will need to spend more time on a brute force attack. 12 is the default value. Depending on how fast your server is you can adjust this value to increase the cost for potential attackers.

> [info]
> **Pro Tip:** Since encrypting passwords will also slow down your tests you might want to make the work factor configurable. You can add a propery to your app instance by adding this line as part of the server setup at the top of `server.py`:
>
> 	  app.bcrypt_rounds = 12 
>
> Then you can pass this variable to the `hashpw` method instead of providing a literal value:
>
> 	  hashed = bcrypt.hashpw(userPassword, bcrypt.gensalt(app.bcrypt_rounds)) 

###Comparing Passwords

Comparing passwords that have been encrypted with bcrypt is very simple. Here's an example from the bcrypt documentation:

	if bcrypt.hashpw(password, hashed) == hashed:
			print("It Matches!")
	 	else:
			print("It Does not Match :(")

All you need to do is provide the unencrypted and the encrypted password to `hashpw`; then compare the result with the hashed password.

#Next Steps

Once you've implemented the modified API described at the beginning of this step the server is complete. From now on we'll focus on implementing the iOS client for Trip Planner.

