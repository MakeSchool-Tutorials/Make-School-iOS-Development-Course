---
title: "Assigned Project Outline"
slug: assigned-project-outline
---

The goal of this project is to introduce you to the most important concepts of iOS development. 

#Project Outline

The goal for this project is to build an iOS app that allows users to plan trips. Users can create trips and trips are defined by a collection of waypoints. Waypoints are represented by a geographic coordinate and a name. Users should be able to create, delete and modify trips.

The content the user creates in the app should be persisted using Core Data. The data should also be synchronized with a backend system that you should write using Python and the Flask framework.

#Data Model

- Trips have a name and a collection of waypoints
- Waypoints have a name (the name returned from the Google Places API) and geographic coordinates

#Feature Outline

- Create Trips by providing a Trip name
- Add Waypoints to trips by searching the Google Places API for a location name
- Display Waypoints on a map
- Remove Waypoints 
- Remove Trips
- Persist all Trips and Waypoints using Core Data
- *Synchronize all Trips and Waypoints with your Server - this should include user authentication so that persisted data is user specific. You however don't need a signup / login screen. It is fine to hardcode the user credentials*

We don't expect all students to complete the last step since a full implementation of a client-server sync can be time consuming. You should however implement at least one API call that successfully works together with your backend (e.g. only syncing new trips but not changes or deletions).

#Screen Layout

Here are mockups of the individual screens the app should contain, including their connections to each other:

![image](TripPlanner_ScreenFlow.png)

The circular image on the screen with the trip overview is another optional feature. You can implement it by using the Google Places API to download an image from one of your waypoint locations.

Feel free to design nicer screens than shown in these mockups! They are primarily concerned with the functionality of each screen, not with the specific design or layout.
