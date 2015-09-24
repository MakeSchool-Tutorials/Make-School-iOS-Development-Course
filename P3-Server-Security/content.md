---
title: "Authentication and Authorization with Flask"
slug: flask-auth
---

#Lecture

<iframe src="//www.slideshare.net/slideshow/embed_code/key/6VtuKX16sLj0GB" width="425" height="355" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe>


#Now it's your turn!

With the lessons learned from the lecture above you should extend your Trip Planner backend. Extend the backend to support the following features:

- Provide an endpoint that allows a client to register a new user by providing username and password
- Provide an endpoint that allows a user to verify credentials (username and password). It should return a status code of 200 (OK) for correct credentials and status code 401 (Unauthorized) for incorrect credentials
- The endpoint that allows clients to create trips should require authentication. The backend should associate the created trip with the authenticated user
- The endpoint that allows clients to retrieve trips should require authentication. It should only return trips that belong to the authenticated user

Remember that it's preferred that you write tests for these new features **before** you write the implementation.


