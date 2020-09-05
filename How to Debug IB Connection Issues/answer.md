It's easy to make bad outlet connections in the Xcode Interface Builder.  This answer addresses a general question (reproduced below) about how to debug these types of issues.

> ### Thread 1: signal SIGABRT
>
> Hi, so I keep getting this error when I try to click on a button to segue to another view controller. I know that this error has to do with an outlet that's not connected properly, but I'm having a hard time figuring out exactly where that issue is. From what I can tell, all of the outlet is properly connected with the correct name. I was wondering if there's a way to get more information about the error or the name of the outlet that's having the problem.
>
> Thank you!

# How to Debug IB Connection Issues

There are a bunch of ways to debug connection issues.  Here are some ways to check your outlets, and some pointers on using the console and responding to different crash behavior.  I'd say **#1** and **#2** are good things to know (I definitely use **#2** all the time),  but when you've got an issue I'd say **#3**, **#4**, and **#5** will help you the most and you might just want to jump right to them.

#### 1. Checking a connection from the file without the storyboard

You can see whether or not elements are connected by looking at the circle to the left of the variable declaration in the file.  If the circle is empty, you've got a deleted outlet!  If the circle has a solid circle inside it, you shouldn't have a deleted outlet.  This can be buggy, and a solid circle doesn't guarantee that you're connected to the right UI element - just some UI element.  If you click on a solid circle however, you'll see a box that tells you in what storyboard and to what element your outlet is connected.  You can click on this box and be taken to the element in the relevant storyboard.  This can be useful.  Xcode automatically opens the Document Outline when it does this, and will show the connected element selected in gray in the outline, and the element will also be selected in the storyboard itself.  Sometimes you have to move the storyboard around to find the selected element when you do this - Xcode doesn't center the storyboard on the element you selected.

![go_to_element_from_file_connection.gif](https://i.imgur.com/9Ume3I8.gif)


And what exactly do I mean by an empty circle?  Here's an example, where the `titleLabel` outlet isn't connected to anything:

![deleted_outlet_demo_in_file.png](https://i.imgur.com/c534bLX.png)

#### 2. Checking a connection from a file using the Assistant Editor (i.e., the file and the storyboard)

Go to your storyboard, and open the assistant editor so that the file where your UI element in question should be connected is open as well.  In the code file, hover over a solid circle.  The connected element in the storyboard will be highlighted in light blue.  I love this highlighting feature - but be wary of it when you're working with nested views because it can be hard to tell exactly what is highlighted because the highlighting does not show up in the Document Outline, just on the storyboard itself.  When working with nested views (and perhaps even just in general) the best method is to use the Connections Inspector *in conjunction with* the Document Outline.  Before we get to that though, here's a gif of checking a connection using the Assistant Editor - you can see the blue highlighting on the storyboard, and the fact that nothing changes in the Document Outline.

![check_outlets_with_assistant_editor.gif](https://i.imgur.com/io1xOH2.gif)

#### 3. Checking a connection from the Connections Inspector

A more robust approach is to use the Connections Inspector, which you can open in your right-hand menu when you are in your storyboard and have an element selected.  It's the icon with a little arrow pointing to the right inside a circle.  It will show you where your element is connected and to what - it's pretty great because it will also list connections to gesture recognizers and actions etc. and you'll be able to see if you have no connections (oops! or, in some cases that's what you want) or perhaps multiple connections (maybe you meant to select a parent element at one point and accidentally selected the child, creating an extra outlet on the object).  If you are working with nested views, open the Document Outline in your storyboard by clicking the little box in the lower left-hand corner, and select the element you're curious about from the menu that opens rather than from the storyboard itself - this way you can be sure you aren't accidentally selecting a view that is on top of the view in question.  

Here's a gif that shows how to do that:

![outlets_with_connections_inspector_and_document_outline.gif](https://i.imgur.com/q0j0JOs.gif)

The Connections Inspector is also usually the safest place to delete connections you don't want anymore.  Here's a gif that shows how to do that.  You just click the little gray "x" by the name of the file your element is connected to:

![view_and_delete_outlets_in_connections_inspector.gif](https://i.imgur.com/dlfmc7I.gif)

#### 4. Fatal error in specific file / unexpectedly found nil
When I deleted the outlet and ran the project, it crashed and brought me to the first place where my code tried to access the disconnected outlet, with this message:

<img src="https://i.imgur.com/wfM2skY.png" alt="crash_due_to_deleted_outlet" width="800">

So if you see this behavior, going to the file where you declare the variable and checking its connection OR going to the storyboard, selecting the variable, and looking at the Outlet Inspector, is a good idea.  If you see it's disconnected, reconnect it.  This CodePath guide on common issues talks about that [here](https://guides.codepath.com/ios/Common-Issues#1-error-unexpectedly-found-nil-while-unwrapping-an-optional-value).

#### 5. SIGABRT in AppDelegate / key value coding compliant
If you're getting a SIGABRT that's bringing you to your AppDelegate, you probably want to take a look at your console and check for output that says something like:

> Error: this class is not key value coding-compliant for the key...

By hitting command + f, you can search for some of those key words (e.g. key, coding, compliant), but it's also useful to look at all the console output because that will give you some info about when you encounter this error, and if there is another issue it might make sense to address first.  If you use the Search or Filter tools on the console, make sure you remember to clear whatever text you put in there, because otherwise you'll think you have no console output next time you run and that can be very confusing - I once spent a long time learning that the hard way!

This guide on common issues has a good demo of this issue and how to solve it under [Common Run-Time Crashes #2](https://guides.codepath.com/ios/Common-Issues#2-error-this-class-is-not-key-value-coding-compliant-for-the-key).

Hope this gives you some ideas about how to debug outlet issues in the future!
