[main][main-link]

# Improvement list

* have a cleaner way of handling the files on the Raspberry Pi
* make sure that there is space enough before recording a video. Investigate how much space is needed
* investigate what additional parameter are needed for the video (resolution and exposure control)
* add number of executed schedules to the list function
* add argument to be able to choose the dir where video are placed on host
* how to control camera settings. https://www.raspberrypi.com/news/raspberry-pi-camera-module-more-on-video-capture/
* tidy up error codes
* add documentation of what is in the .rpi directory
* add section about how rpi uses cron
* add function to setup a video with custom cron schedule (to support non-daily schedules). Use the function from the daily scheduling functions. This may require renaming of the video files...
* find a way to get the IP for the Raspberry Pi without having to run the raspberry Pi with display/keyboard/mouse
* add function to show rpi log file
* DONE -- check that the Raspberry Pi device has been setup when calling the other functions
* DONE -- have a handler to make sure that concurrent uses of the Raspberry Pi do not collide
* DONE -- cron setup helper functions
* DONE -- cleanup command naming for schedule
* DONE -- add function to retrieve the videos
* DONE -- add client to authorized_keys as part of setup
* DONE -- add raspberry pi serial number to saved videos
* DONE -- place the scheduled video files somewhere a bit cleaner
* DONE -- fail when trying to create the same schedule twice
* DONE -- warning when adding a schedule overlaping with another schedule
* DONE -- tidy up the schedule stuff: different formats "HH:MM duration" (user friendly) "HH:MM_duration" (script friendly - no spaces) "MM HH * * * ...duration..." (cron)
* DONE -- add a function to fetch the scheduled videos (maybe including deleting them...)
* DONE -- currently, the chosen key is added to authorized_keys for every rpiSetup. Maybe remove duplicates?
* DONE -- Update the entry README.md. Maybe split the scripts/README.md into a README per section to make it easier to find information...
* DONE -- add function to list the videos on host and raspberry pi
* DROPPED -- use tool to create a proper rpi server (like numbat does). This is dropped for now as it would add a lot of code to maintain without much value.
* DROPPED -- make the calls from host to rpi a bit less stupid: i.e. use FUNCNAME instead of duplicating it in ssh. More hastle and untransparency than benifit
* DROPPED -- maybe make the functions work on rpi as on remote host... Or is it too much abstraction? Not really needed

[main-link]: ./README.md