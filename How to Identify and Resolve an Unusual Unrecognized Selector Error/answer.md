This answer addresses a crash caused by an unusual occurrence of the unrecognized selector sent to instance error.  The student question is reproduced below.

> ### Unrecognized Selector Sent to Instance
>
> Hi everyone. I didn't want to post here because I wanted to see if I can find a solution to this online or on my own, but I've been getting different answers and solutions. However, nothing has helped and it made me more confused.
>
> I am getting a SIGABRT error whenever I try to build and run my code. I looked at the output and see the following:
> ![screen_shot_of_error.png](https://i.imgur.com/sQA9mME.png)
>
> I am thinking I didn't properly set up the IBOutlet, but I tried and it didn't do anything. I found that this problem showed up once I tried to fix the problem of the data not loading by putting in the codes: self.tableView.reloadData()

# How to Identify and Resolve an Unusual Unrecognized Selector Error

### What's Going On

The thing that tipped me off to your issue was the error

> [UITableViewContentView setText:] unrecognized selector sent to instance

When you have a UI element that has a set-able `text` attribute, such as a UILabel, when you say `myLabel.text = (whatever you want to set the text to)`, UIKit runs some Objective-C code that uses selectors, and tries to select and run a function `setText` on your variable `myLabel`.  Somehow, this method got called on an instance of UITableViewContentView, which doesn't recognize the selector `setText` because it doesn't have a text attribute.

The code in your NowPlayingViewController.swift and your movieCell.swift files is all good 👌.  But you accidentally connected the content view to your `titleLabel` in your movieCell class.  In the screen shot below, I opened the Document Outline on the left of the storyboard, selected the first child of the table view cell which is the content view for the cell, and opened the Connections Inspector on the right side of the Xcode IDE.  You can see in the Connections Inspector that the element selected is an outlet with the name "titleLabel" in the file moviesCell.  If it's hard to see, right click on the image and select "View Image" - that should make it larger than can be embedded here:

![content_view_is_outlet.png](https://i.imgur.com/ipL4kSA.png)

You'll basically need to disconnect this and connect the correct element - I've put instructions for that below.  One thing you'd want to check though before proceeding is that the correct element isn't connected to something funky.  This is also a good way to confirm a hunch that you just selected the wrong thing.  Typically, the desired element just didn't get connected to anything, which we can confirm is the case here by selecting it in the Document Outline and checking the Connections Inspector:

![title_label_no_connections.png](https://i.imgur.com/DHHlMDR.png)


I was surprised that this happened because in your movieCell file, the outlet is declared as a UILabel, not a UITableViewContentView.  If it had been declared as a UIView (which is what happens automatically when you control-drag from a cell's content view to file), Xcode would have given you an error before you built, as soon as you try to set the text attribute in the `cellForRowAt` method of your NowPlayingViewController. Xcode would have said "Value of type 'UIView?' has no memeber 'text'" in red on line 54 when you say `cell.titleLabel.text = title`.  So it's interesting that Xcode didn't detect the fact that while the outlet was declared to be of type `UILabel` (which does have an attribute `.text`), it was really a type of UIView all along.  You may have had the right thing connected initially and then at one point changed things and accidentally re-added the wrong thing and it didn't update the type, or you saw that the type was wrong and just changed it in the file by hand without also updating the connection.  Either way, it is super easy to select things that are inside or on top of a view when you select them from the storyboard so it's good to know how to use the Document Outline.  I recommend using the Document Outline if you're making outlets with nested views.  It's hidden away typically, but you can open it (and then hide it again for more storyboard real estate) by clicking the little box in the lower left-hand corner of the storyboard:

![document_outline.png](https://i.imgur.com/ez2HuFz.png)

### How To Fix It

1. Open the Document Outline on the left of the storyboard
2. Selected the first child of the table view cell which is the content view for the cell
3. Open the Connections Inspector on the right side of the Xcode IDE
4. Delete the connection to the content view by clicking the "x" on the left side of the bubble that says "movieCell" in the Connections Inspector
5. Using the Document Outline, select the title label, which is currently auto-filled with the placeholder text in the label, "Wonder Woman".  The selected element will highligh in light blue.  Since there aren't many overlapping elements in your storyboard, you'll know you've got the right thing if it looks like the title label is highlighted.
6. Control + drag from the title label in the Document Outline to line 15 of your movieCell to reconnect the right UI element with the `titleLabel` UILabel variable you have set there

Then it runs just fine - take a look:

![post_fix_simulator.png](https://i.imgur.com/v9Crf8G.png)

It looks good - nice work!  Your next task will be to work with AlamofireImage and use the workspace created to set up the poster image.  Let us know if you need any help with that.  Or if you still have questions about this, post a comment here.  And if this solved your issue, mark the answer as approved and set the issue to "Resolved" ✅
