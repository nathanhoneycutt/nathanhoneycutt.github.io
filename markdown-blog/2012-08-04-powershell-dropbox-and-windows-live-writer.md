# PowerShell, Dropbox, and Windows Live Writer

I think I have a problem. I cannot use just one computer. I have work computers, home computers, and computers I use exclusively on the couch while watching TV. I really like to keep all of these computers in sync so I have all of my files, programs and configuration no matter where I am. For the most part, Dropbox lets me do that. But every now and then, something like Windows Live Writer comes along that with a hard coded save path.

Fortunately, Gabriel Novo shows the solution using Junctions and third-party downloadable tool. That’s great, but surely there’s a PowerShell way to do this, right? Turns out the answer is yes, provided you have PowerShell Community Extensions installed. If you don’t, stop reading right now and get it installed.

The answer is as simple as PSCX’s New-Junction cmdlet. The parameters are named backward from what I initially interpreted them to be, but that’s not a problem. LiteralPath points to the “fake” folder you’re making, and TargetPath points to the folder to which you’re linking. So given that my Windows username is Nathan and I have everything installed to the default locations, I simply run:

```powershell
New-Junction ` 
     -LiteralPath 'C:UsersNathanDocumentsMy Weblog Posts' ` 
     -TargetPath 'C:UsersNathanDropboxMy Weblog Posts'
```

Done. I now have Windows Live Writer unknowingly syncing local drafts to Dropbox. That’s on one system of course, so you’ll have to do that on each system you use. Or do you? Find out in the next post, where I continue blogging about blogging.