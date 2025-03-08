# A Pomodoro Timer in PowerShell

This post falls under the heading of things that probably don’t make much sense, except it was fun and I wanted to do something very specific using a very specific technology. Rather than run yet another program in my task tray (which mostly irritates me because of icon clutter and not system resources), how could I just use another tab in my PowerShell window to help me use The Pomodoro Technique? I came up with the following function that I’ve placed in my $profile.

```powershell
function Start-Pomodoro
{
    Param (
        [int]$Minutes = 25
    )
 
    $seconds = $Minutes*60
    $delay = 15 #seconds between ticks
 
    for($i = $seconds; $i -gt 0; $i = $i - $delay)
    {
        $percentComplete = 100-(($i/$seconds)*100)
        Write-Progress -SecondsRemaining $i `
                       -Activity "Pomodoro" `
                       -Status "Time remaining:" `
                       -PercentComplete $percentComplete
        Start-Sleep -Seconds $delay
    }
 
   $player = New-Object System.Media.SoundPlayer "C:\Users\me\Dropbox\Music\CTU.wav"
    1..6 | %{ $player.Play() ; sleep -m 1400 }
}
```

What does this do? For starters, in my now-dedicated tab, I can just run “Start-Pomodoro” and get a 25-minute timer going, with a progress bar that shows how long I have remaining in my current pomodoro. At the end, it repeats the CTU ringtone from “24″ (which is just irritating enough to get my attention). I had to tinker with the delay on the sleep command so that the clip had time to complete before restarting, so you will need to do the same with whatever audio clip you use.

If using an external audio clip isn’t your speed, you can just use whatever you have for system sounds. Just replace the two audio-playing lines above with something like the following.

`1..10 | %{ [System.Media.SystemSounds]::Exclamation.Play() ; sleep -m 750 }`

Like I said, I don’t know how useful this is. But it was fun, and I use it pretty much all day.
