# Anobe

Anobe nukes all Adobe processes from orbit whenever you're done using them.

A number of modern software vendors feel that because you are ~willing~ forced to pay rent for their now-industry-standard software, this entitles them to permanently hog your hardware without oversight or visibility.  It's only moderately savvy users who might ever notice that Adobe is unnecessarily running double-digit numbers of unique, non-trivial background processes at all times, either sending computer telemetry back themselves, or simply leaking memory to absurd degrees during a soak of more than, say, 48 hours.

(I have personally caught the Adobe Desktop Service — basically the "do I need to update" poller — leaking over a gigabyte of memory after around four to five days of uptime).

## macOS

Store this in your `.bashrc` as a one-line alias or as a reusable function.  Do not use `adobe` in the function/file name, it will terminate itself mid-stream.  Perhaps `anobe` instead?

```bash
rm -f ~/Library/LaunchAgents/com.adobe*; sudo kill -9 $(ps aux | grep -i '[a]dobe' | awk '{print $2}')
```

The script first wipes the LaunchAgent files, preventing Adobe from firing up, with no oversight or permission from the user, all their telemetry and monitoring junk at CPU boot. These files are recreated whenever any Adobe program is run, no matter which one begins the chain.

The script then loops through all open processes containing `adobe` in their filepath and feeds them back into `kill -9`, which is the low-level nuclear banhammer version of Force Quit.

## Windows

Store this as a PowerShell function and run it from an elevated prompt.

```powershell
wmic process where "ExecutablePath like '%%Adobe%%'" call terminate
```

It functions much the same as the macOS version, although it currently does not prevent startup items from being secretly set in the Scheduler.

The `terminate` seemed to be missing two or three processes when run, but I discovered through testing that running it multiple times will eventually clear everything up — the sledgehammer approach to software management.

When the `process` call spits out `No instances available`, you've won. Three runs seems to be the charm at time of writing, but from time to time it takes more to guarantee Success™. If you do not see `No instances available` toward the end of the output, simply run again. _It will eradicate all eventually._
