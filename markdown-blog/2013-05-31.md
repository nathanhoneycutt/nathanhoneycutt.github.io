# PowerShell for Fun and Irritation

If you’re a fan of the show “Family Guy”, you may remember the joke where Robert Loggia was asked to spell his name. He did so by working his name into a sentence for every letter. For example, “E as in everybody loves Robert Loggia.” We’ve started doing that around the office, with the challenge being can you come up with the verbose spelling fast enough for it to still be relevant. I quickly decided that PowerShell could help me with this. There’s no trick here, really. I break up the incoming text to a character array, and for each letter I reference into a hash table of sentence beginnings to go with each letter.

```powershell
function Get-VerboseSpelling
{
    Param(
    [string]$Text
    )
     
    $spellingDictionary = @{
    "A"="Awesome,";
    "B"="Boy howdy,";
    "C"="Certainly you now know";
    "D"="Don't you know";
    "E"="Everybody knows";
    "F"="Feel good about yourself because you know";
    "G"="Good thing you now know";
    "H"="Have you heard";
    "I"="I have now told you";
    "J"="Just accept that";
    "K"="Knowledge has been passed because you know that";
    "L"="Look, just accept that";
    "M"="Man, aren't you glad you now know that";
    "N"="Now you know that";
    "O"="Other people know that";
    "P"="Please rember that";
    "Q"="Quick, remember that";
    "R"="Remember that";
    "S"="Stop, collaborate and listen,";
    "T"="Thank you for remembering that";
    "U"="Ultimately, just accept that";
    "V"="Very smart people know that";
    "W"="Why won't you accept that";
    "X"="Xenobiologists know that";
    "Y"="Your significant other already knows that";
    "Z"="Zebras know that";
    }
 
    $charArray = $Text.ToUpper().ToCharArray();
 
    foreach($char in $Text.ToUpper().ToCharArray())
    {
        $char = $char.ToString()
        if($char -ne ' ') {
            "{0} as in `"{1} it's {2}`"" -f $char, $spellingDictionary[$char], $Text
        }
    }
}
```

The real humor here comes when you pair this with the PowerShell Community Extensions’ cmdlet Out-Speech. You can you type something in and have the verbose spelling read to you. The default voice in Windows 7 doesn’t do a particularly good job of it, but it’s still fun.

I’m already seeing ways to improve this, such as having each letter defined with multiple sentence starters where the script will pick one randomly. If you do anything neat to build on this, I’d love to hear about it!