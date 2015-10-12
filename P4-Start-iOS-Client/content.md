---
title: "Starting on the iOS Client"
slug: trip-planner-ios-client
---

#GitHub Classroom Assignment

[Start the assignment by using this link.](https://classroom.github.com/assignment-invitations/2f5a4dabf8ab6ab4eba5469c5325ed8f)

#Starting on the iOS Client

Now that the basic API server is complete, it is time to start working on the iOS client. In general you can approach the project in any way you prefer - your main goal is to build an app that conforms to the spec that we provided you with at the beginning of this track.

However, we have a lot of lectures about different aspects of iOS development that go along with this project. Therefore we want to suggest an order in which you tackle the implementation. If you follow that order the lectures on a specific topic will be delivered more or less around the time when you are working on a relevant feature.

Here's the recommended order:

1. **Start by bulding the entire UI**, based on the provided mockups, without any functionality
2. **Add your data model using Core Data**. Then add dummy logic to the UI that you've created. E.g., instead of letting the user search for real locations, you can always return the same location from the search and display it on the map
3. **Integrate the *Google Places* API** to search for real locations instead of providing dummy data.
4. **Add a feature that synchronizes all created trips with your backend**. While these trips need to be user specific, you don't need to implement a login/signup UI; you can hardcode the credentials into your app

