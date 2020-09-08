This answer provides a fix for the various errors raised by the student's code.  Along the way, I walk the student through my debugging process to give an example of how they might approach these types of issues.  I explain some key points about Xcode and the particular errors raised.  Lastly, I give the student a hint on the issue they'll face once their app is running.

The student question is reproduced below, but does not provide much information.  I used additional conversations with the student and the student's repo as context.

> ### Yelp Assignment
>
> Okay so I made all the changes, but I only get past the main page I am not sure what to do because I added to the custom class but it wont save.
Any photos and steps would be great.

# How to Resolve a Couple Unknown Class Errors and the Classic Key Value Coding-Compliant Error

## Finding the Errors

When I ran your app, it showed the launch screen successfully and then crashed, just like you said.  When it crashed, it did the following two things, which Xcode commonly does when the simulator can't execute a given instruction in your code:

1. Opened the AppDelegate.swift file and showed a SIGABRT error.  This isn't very informative in our case.  Luckily this isn't the only thing Xcode provides for error info.
2. Opened the console and showed some error messages.  This is very useful.

It looked like this:

![xcode_sigabrt_crash.png](https://i.imgur.com/r0zXvDE.png)

I expanded the console to take up as much space as it can, and found a few interesting things:

<img src="https://i.imgur.com/yl00YTl.png" alt="first_run_errors" width="1000">

There are three errors:

1. Unknown class BusinessCell in Interface Builder file
2. Unknown class \_TtC4Yelp13Business_Cell in Interface Builder file
3. \*\*\* Terminating app due to uncaught exception 'NSUnknownKeyException', reason: '[<UITableViewCell 0x7ffbb304f600> setValue:forUndefinedKey:]: this class is not key value coding-compliant for the key NameLabel.'

One good thing to understand about the simulator is that it prints errors as it encounters them, so the output in the console is chronological from top to bottom.  That means that the error at the top is the first error it encountered when it tried to run.  Then it tried to keep going, and it came across error 2 and printed a note about that in the console.  Finally it logged a third error to the console and that third error caused it to crash.  At this point it stopped printing anything to the console, because it wasn't running anymore - it said it was "Terminating app due to uncaught exception" and accordingly, it aborted and gave the "SIGABRT" message in the app delegate.  You can't see the end of the console output in the screenshot above because my mac screen is smallish, but that's what happened.  Here's a clip of the end of the output and the lldb it prints as its last goodbye:

![Screen Shot 2018-09-20 at 7.40.12 PM.png](https://i.imgur.com/PIvE2rE.png)

## Fixing the Errors

You might think we should start with the last error, since that seems to be the one that finally made it crash.  But sometimes starting with the earlier errors can clear up the issues it was having later in its run.  Let's look at the issues one by one, starting with the first.

### 1. Unknown class BusinessCell in Interface Builder file

This issue usually means you have an object in the storyboard that is supposed to be of a certain type, in this case BusinessCell, but you haven't assigned the class of that storyboard object successfully.  There's a CodePath guide on common issues, and it talks about this particular issue [here](https://guides.codepath.com/ios/Common-Issues#3-error-unknown-class-some-viewcontroller-in-interface-builder-file).  We know that it's the custom cell in the BusinessesViewController table view that should be of class type BusinessCell, so let's take a look and see what its class assignment is.  

To do this, go to the storyboard and follow these steps:

1. Open your Document Outline
2. Select the custom cell in your Businesses View Controller
3. Open the attributes inspector
4. Look at what is set for the Custom Class

![error_1_unknown_class_BusinessCell_fix.png](https://i.imgur.com/hneuJSd.png)

Well, it looks like its custom class is set to "Business_Cell".  You must have originally named the class "Business_Cell" with that underscore, and then changed it later on but forgot to change the custom class type for the cell.  Open the dropdown and change it "BusinessCell" without the underscore, to match the class file you have now.  Let's give it a run and make sure we got rid of error 1.

The app runs!  Oddly enough, the error "Unknown class BusinessCell in Interface Builder file" is still there.  I tried a few things and I haven't got it to go away, but there doesn't seem to be any adverse behavior.  If you're encountering an issue just comment on this answer and we'll continue to troubleshoot.


### 2. Unknown class \_TtC4Yelp13Business_Cell in Interface Builder file

Magically disappeared when we cleared up the first issue!


### 3. \*\*\* Terminating app due to uncaught exception 'NSUnknownKeyException', reason: '[<UITableViewCell 0x7ffbb304f600> setValue:forUndefinedKey:]: this class is not key value coding-compliant for the key NameLabel.'

Magically disappeared when we cleared up the first issue!  This was happening basically because xcode didn't know what the `NameLabel` attribute was since it was using "Business_Cell" which didn't exist and therefore didn't have any attributes like `NameLabel` or anything else for that matter.  But sometimes this error can happen when you've got a broken outlet.  There's a CodePath guide to [Common Issues](https://guides.codepath.com/ios/Common-Issues) and it has a concise solution for the issue ["this class is not key value coding-compliant"](https://guides.codepath.com/ios/Common-Issues#2-error-this-class-is-not-key-value-coding-compliant-for-the-key) if you want to learn how to fix that for the future.


----

Hope this helps - let us know if you have more questions, again providing as much information as possible, and if this resolved your issue, mark this question as resolved ✅  Also mark this as the approved answer by hitting the big check mark, if it solved your issue, so that anyone who might be working through a similar problem as you had knows which answer worked for you.


#### One Last Note
Once you fix your BusinessCell error above, you'll notice that your tableview rows are pretty short and kind of cut off your content.  To fix this, go to your BusinessesViewController.swift file and add a line "`tableView.rowHeight = 200`" so that you can see what's in your cell while you're doing the layout.  I think that the videos will tell you how to do dynamic height etc, but this line might be useful to you for now.  Right now your app is using the default tableview row height, which is pretty short:

![Screen Shot 2018-09-20 at 8.55.48 PM.png](https://i.imgur.com/6o4vFmh.png)

We've got a bunch of posts about row height.  Here's one [answer](https://discussions.codepath.com/courses/ios_university/questions/image-loading-problem) that includes links to two more in-depth answers, and [here's one](http://discussions.codepath.com/courses/ios_university/questions/content-in-the-cell-aren-t-scaled-properly) linked to in that answer that I wrote and I know touches a little bit on how this relates to autolayout.

It is running though - nice work!!
