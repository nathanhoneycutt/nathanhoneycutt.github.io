# PowerShell, Dropbox, and Windows Live Writer (Part 2)

In a [previous post]({% post_url 2012-08-04-powershell-dropbox-and-windows-live-writer %}), I described how to setup a file system junction to make Windows Live Writer save to Dropbox without knowing it. That solution worked if setup on each computer that I use. However, I am a huge fan of having the PowerShell profile I stash in Dropbox keep track of this type of configuration. The solution was fairly simple. First, the script snippet:

```powershell
if( (Get-ChildItem $HomeDocuments "My Weblog Posts" | Select-Object -Property Attributes | Select-String "ReparsePoint") -eq $null)
{
    [void](New-Junction -LiteralPath "$HomeDocumentsMy Weblog Posts" -TargetPath "....DocumentsMy Weblog Posts")
}
```

Let's break that down. I'm checking for the existence of a “My Weblog Posts” folder in my local documents folder. If one is not found, it creates a junction to the Dropbox folder.

_“But Nathan, what happens if you already had a Windows Live Writer installed and that folder existed?”_

Good question. This depends on my having PowerShell setup and running on any new computer I use prior to installing or using Windows Live Writer. The way I work, I can 100% guarantee that both PowerShell and Dropbox are setup before I even think about things like Writer. Your workflow may be different, but you can pretty easily adapt this snippet to handle that. If I ever run into a situation where I Writer configured before PowerShell, I can just remove the folder manually and then let PowerShell take over. That feels safer to me than any script that would just blindly remove that folder, in case I had any local drafts saved that I might not want to delete.
