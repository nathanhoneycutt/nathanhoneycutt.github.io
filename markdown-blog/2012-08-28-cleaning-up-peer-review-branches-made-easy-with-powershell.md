# Cleaning Up Peer Review Branches Made Easy with PowerShell
Maybe I’m in the minority, but whenever I start working on a peer review, the first thing I do is create a branch in git. That way if I have any comments or edits, I already have everything setup and I don’t have to lose my train of thought by closing the solution, create a branch, re-open the solution and hope I remember what I was going to say.

I’m not, however, very good at cleaning up those branches when I’m done. After our last sprint, I had 45 orphaned peer review branches. Because I use a consistent naming scheme (peer-StoryID), this could either be a lot of typing, or I could use PowerShell to make it easy.

`git branch | sls "peer" | %{ git branch -d $_.Line.Trim() }`

That will grab the list of all branches that contain the text “peer” and remove them. The extra `Trim()` is because I was getting branches names prefixed by 3-5 spaces from the output.

Can you think of a better way to do this? Post it in the comments below!
