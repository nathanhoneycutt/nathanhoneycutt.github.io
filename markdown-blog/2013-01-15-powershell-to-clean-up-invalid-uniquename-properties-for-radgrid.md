# PowerShell to Clean Up Invalid UniqueName Properties for RadGrid

In a recent upgrade to our product, we discovered that a great many of our custom reports had UniqueName properties that were incompatible with the new version of Telerik’s RadGrid. The question fell to me, “how many reports have spaces in their UniqueName property?” Fortunately, PowerShell made it fairly simple to identify the reports with problems.

```powershell
Get-ChildItem -Path . -Filter *.aspx -Recurse | 
  Select-String -Pattern "UniqueName=`"\w+\s\w*`"" | 
  Group-Object -Property Path | 
  Select-Object -Property @{Name='Spaces in Name';Expression={$_.Name}}
```

Wonderful, I have identified the problem. It should come as no surprise to any developer that the next step was “great, can you fix it?” I went through a few files by hand to verify that any kind of mass replace was unlikely to cause issues. Once I was sure it was “safe” (as safe as blindly replacing property values can be), PowerShell made a day-long job take only minutes.

```powershell
$reports = Get-ChildItem -Path . -Filter *.aspx -Recurse |
    Select-String -Pattern "UniqueName=`"\w+\s\w*`"" |
    Group-Object -Property Path |
    Select-Object -Property Name
 
foreach($report in $reports)
{
    $aspxPage = $report.Name
    $codeBehind = "{0}.vb" -f, $report.Name
 
    $uniqueNamesWithSpaces = (Select-String -Path $aspxPage -Pattern `
      "UniqueName=`"(?<uniqueName>\w+\s\w*)`"" `
      | Select-Object -Expand Matches)
 
    $aspxContent = (Get-Content -Path $aspxPage)
    $codeContent = (Get-Content -Path $codeBehind)
 
    foreach($uniqueName in $uniqueNamesWithSpaces)
    {
        $aspxContent = $aspxContent -replace `
          $uniqueName.Groups["uniqueName"].Value, `
          $uniqueName.Groups["uniqueName"].Value.Replace(" ", "")
 
        $codeContent = $codeContent -replace `
          $uniqueName.Groups["uniqueName"].Value, `
          $uniqueName.Groups["uniqueName"].Value.Replace(" ", "")
    }
 
    Set-Content -Path $aspxPage -Value $aspxContent
    Set-Content -Path $codeBehind -Value $codeContent
}
```

And that’s all there is to it. Any custom report that has a RadColumn defined with a UniqueName property containing a space would magically have that space removed, both in the ASPX page and the codebehind file. What about you? How would you have handled this differently? One of the great things about PowerShell is that there’s no One Way to handle everything, so post your solutions below.
