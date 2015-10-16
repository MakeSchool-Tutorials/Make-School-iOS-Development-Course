---
title: "Multithreading on iOS"
slug: multithreading-ios
---

#Lecture

<iframe src="//www.slideshare.net/slideshow/embed_code/key/4AD0jH2YT32LT5" width="100%" height="400" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe>

[Download the slides here.](https://s3.amazonaws.com/mgwu-misc/MS-17/Slides/Threading.pdf)

#Exercise

The exercise is to take the starter project that performs some long running tasks on the main thread and unblock the UI by using various different approaches:

1. Create a non-blocking version of the app that updates the progress label correctly using GCD
2. Improve the performance of the app by submitting each operation as an individual block, ensure that the “Completed” message is still displayed correctly
3. Refactor the code to use GCD groups to determine when all tasks have completed
4. Increase the operation count from 20 to 1000 and add buttons to start and cancel operations (hint: use NSOperationQueue)

[Start the assignment here.](https://classroom.github.com/assignment-invitations/7f40184f2127b234bcb1d82114b4b8bd)