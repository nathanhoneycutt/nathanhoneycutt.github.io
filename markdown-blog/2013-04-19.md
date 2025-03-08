# Maintaining Custom web.config Elements During an Upgrade

We have been using PowerShell to perform low-friction updates to our intranet application for several months. This lets any of our DevOps staff build an update package, deploy it to the client system and run a single command to update the application. This has worked well for us, until we finally bumped into our first client site that had a web.config file different from the others. In this case, it was the presence of an `<identity impersonate=”true” />` tag. After we performed an update at this site, the setting was lost and the application stopped working.

This is clearly not acceptable, but we also do not need to maintain two (or more) copies of a stock config file just to account for a single setting. Fortunately, PowerShell’s powerful handling of XML comes to the rescue. Not only does XML data get treated like an object in PowerShell (so you can do `$myXml.ParentNode.ChildNode.AnotherChild`), but you have access to all of the XDocument methods you’re used to in C#.

Before performing the update, we want to check the config file for the `<identity>` tag, and grab a copy of the node. Not the text, but the node itself. This ensures that whatever attributes are defined come with it, without us having to do anything extra. That means that “impersonate=true”, “impersonate=false”, “username=blah”, etc. will all be migrated without having to manually handle each possibility. We then update the web.config file inside of the update package, before deploying it. This ensures that some kind of crash during the update (such as after copying over the new website but before updating the web.config) won’t leave the client’s system in an invalid state. We do this by inserting a copy of the existing `<identity>` tag.

There is one big gotcha here. When you try to insert a node from one XDocument to another, it complains. The fix for this is to perform an ImportNode() first, then append the node to the new document. Also note that AppendNode() will put the `<identity>` at the end of `<system.web>`, not necessarily in the same position it was before. If node order matters for your XML document, be aware of this. If the order of sibling nodes matters for your XML document, I’d argue that you might have a different problem anyway.

After all of that, here’s the code we used. Hopefully it proves useful to someone.

```powershell
function MigrateIdentityTagsInWebConfig
{
    Param(
        $Source,
        $Destination
    )
 
    foreach($webConfigFile in (Get-ChildItem -Path $Destination -Filter web.config -Recurse))
    {
        $oldXml = [xml](Get-Content $webConfigFile.FullName)
        $identityElement = $oldXml.configuration."system.web".identity
 
        #Did we have an identity element in the existing web.config file?
        if($identityElement)
        {
            $correspondingSourceFile = $webConfigFile.FullName.Replace($Destination, $Source)
            $newXml = [xml](Get-Content $correspondingSourceFile)
 
            #Make a copy of the node from the existing web.config, import it into the new, and then append it
            $newIdentityElement = $newXml.configuration."system.web".OwnerDocument.ImportNode($identityElement, $true)
            [void]$newXml.configuration."system.web".AppendChild($newIdentityElement)
 
            $newXml.OuterXml | Format-Xml | Set-Content -Path $correspondingSourceFile
        }
    }
}
```
