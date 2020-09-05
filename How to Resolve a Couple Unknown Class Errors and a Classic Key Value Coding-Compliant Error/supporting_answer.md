In this answer, I explain why the student only sees one line of text in their app where they expect to see five lines.  

The original question and brief comment thread are reproduced below.  I replaced the student name and username with "Student" for privacy.

> ### Content in the cell not scaled properly
>
> As you can see in the story board, I have a label that contains 5 lines of text, but they aren't scaled properly when I run the simulator.
>
> #### Comments
> **hlpostman:**  Hey Student!  I'll take a look :)
>
> **hlpostman:**  Quickfix: on line 44 of your ViewController.swift, set `TableView.rowHeight` to a number such as 150 instead of `UITableViewAutomaticDimension`. There are other answers as well, and your question touches on both table views and labels (you were having an issue with row height cutting off the label - but it's also possible to have a label cut off its own text). I'll get together some info and screen shots and post a fuller answer. In the meantime though, this "quick-fix" will let you see what you're pulling so you can keep coding 👍
>
> **Student:**  Thank you so much Hannah.  Sorry for the late response.

# Answer

If you're not using AutoLayout, you need to set the `height` property of your table view statically in the view controller that manages the table view.    Currently, the view controller you have managing the main movies table view sets the height like this, on lines 43 and 44:


<img src="https://i.imgur.com/AC6gqqk.png" alt="bug_image" width="800">


When you're using AutoLayout, `UITableViewAutomaticDimension` will automatically calculate row height based on cell content.  But without the AutoLayout constraints, the row doesn't really understand what it will be responsible for accommodating.  The row height you see on the simulator in the screen shot above is an XCode default for table views that don't have a row height set at all - if you were to build your project with lines 43 and 44 commented out, it would look the same as it does now:


<img src="https://i.imgur.com/YHfs3NZ.png" atl="no_height_settings" width="800">

So set `rowHeight` to a value.  You can play around and decide what value makes the app look best to you.  For this assigment, 150 usually works nicely.  Here's what that looks like:

<img src="https://i.imgur.com/8QZJpEX.png" alt="the_fix" width="800">

Note that we didn't need line 43 at all to get that nice output.  That's because the `estimatedRowHeight` property is another thing that's only useful when using AutoLayout.  Remember `UITableViewAutomaticDimension` that calculates the row height based on the cell content?  Well, the calculation is a rather expensive task.  Setting the  `estimatedRowHeight` property can speed up height calculation process by giving `UITableViewAutomaticDimension` a ballpark.  You'll often see code that sets `estimatedRowHeight` to a number (such as 150), and then sets the actual `rowHeight` to `UITableViewAutomaticDimension`.  You'll be doing this in some upcoming labs and assignments.

I'll comment on one last thing.

You mentioned that your movie overview label contains 5 lines of text, but isn't "scaling".  The reason your overview label isn't showing up is because of the row height, as discussed above.  But you weren't wrong to wonder if the issue was the overview label itself.  Labels can be very tricky.  In particular, the number of lines of text you have in the label on your storyboard doesn't translate to what it will load in at run time 😕.  If you want to have a maximum of exactly 5 lines of text in the label, go to the attributes inspector and set "Lines" to 5.  Right now, your attributes inspector is showing that "Lines" is set to 0.  This will make your label display as many lines as it can fit.  If you want a maximum of 5 lines, go ahead and set "Lines" to 5 in the attributes inspector (a menu on the right side of Xcode).  

Note that you can tell XCode to handle data that doesn't completely fit in different ways.  Right now you have Autoshrink set to "Minimum Font Size" at 0.5.  When there is too much text, XCode will make your data's text size smaller than the 12 point you have set, as long as it doesn't get smaller than 6 point, and try to squish in as much text as it can into your 5-line limit with this smaller font.  That looks like this:

<img src="https://i.imgur.com/7AEXUz2.png" alt="min_font_scale_and_simulator" width="800">

If you want to always have the font size at 12, you can change the Autoshring setting to "Fixed Font Size".  When there is too much text, XCode will truncate and add an ellipses.  It looks like this:

<img src="https://i.imgur.com/qUMrInz.png" alt="fixed_font_size_and_simulator" width="800">
