[main][main-link]

# RPI Functions Reference

## Host functions

### rpiSetup

```
rpiSetup <rpi ip address> <username> [<ssh key>]
```

This bash function sets-up the information needed to connect to the Raspberry pi device and makes it possible to access it without repeating these information on every access.
Calling this is necessary to be able to use the other functions described here.
Note that there is currently only support for one such device.

The information about the Raspberry Pi device are stored inside the .rpi directory in the home of the user who calls rpiSetup.

`rpi ip address`: IP address of the Raspberry Pi device to communicate with.

`username`: The name of the user to use on the Raspberry Pi device.

`key`: key to use when ssh'ing with the Raspberry Pi device. This defaults to ~/.rpi/rpi_rsa. If the key is not present, it will be created byt the rpiSetupFunction. The key is sent to the Raspberry Pi device to avoid being prompted a password on each ssh connection to the Raspberry Pi device. That key is also used for all the rsync functions provided.

### rpiGetAccess

```
rpiGetAccess
```

This bash function prints on stdout the access string <username>@<ip address> for the Raspberry Pi device that has been setup using [rpiSetup](#rpiSetup). It is mainly thought of as an internal function.

### rpiGetSshOptions

```
rpiGetSshOptions
```

This bash function returns the options to be added to ssh commands for using the key specified during rpiSetup.

### rpissh

```
rpissh [<command>]
```

This bash function is used to ssh to the Raspberry Pi that has been setup using [rpiSetup](#rpiSetup).

`command`: optional command to execute on the Raspberry Pi device. If command is passed, then the command is executed on the Raspberry Pi device (make sure to use quotes and escape elements in the command as needed). If command is omitted, a shell will be created on the Raspberry Pi device allowing interaction with it.

### rpiGetSerialNumber

```
rpiGetSerialNumber
```

This bash functions returns the serial number of the Raspberry Pi that has been specified when calling rpiSetup.

### rpirsync

```
rpirsync <source> <destination>
```

This bash function rsyncs files from source to destination. Files on the Raspberry Pi device have to be prefixed with the user to use on the Raspberry Pi device and the Raspberry Pi device IP address.

`source`: file/dir to rsync

`destination`: where to copy file/dir

Example:

```
rpirsync myFavouriteDir/ raspberrypi@192.168.0.77:myFavouriteDirOnRpi/
```

### rpirsyncFrom

```
rpirsyncFrom <path>
```

This bash function rsyncs from the Raspberry Pi setup using [rpiSetup](#rpiSetup) to the machine that the function is run on.

`path`: path that should be rsynce'ed. The path is relative to the Raspberry Pi user home and is copied to the same path relative to the current work directory on the host machine.

### rpiCapturePhoto

```
rpiCapturePhoto <file>
```

This bash function takes a photo on the Raspberry Pi setup using [rpiSetup](#rpiSetup) and transfers the photo back to the machine running the function.

`file`: name of the file that the photo should be saved as.

### rpiCaptureVideo

```
rpiCaptureVideo <file> <time in seconds>
```

This bash function makes a video on the Raspberry Pi setup using [rpiSetup](#rpiSetup) and transfers the video back to the machine running the function.

`file`: name of the file that the video should be saved as.

`time in seconds`: number of seconds that the video should be recorded for.

### rpiSchedVideoAddDaily

```
rpiSchedVideoAddDaily <time as HH:MM> <duration in seconds>
```

This function schedules a video of <duration in seconds> to be recorded daily at HH:MM. It will fail if another
video is already scheduled for the same time and return error code 1.

`time as HH:MM`: time at which a video should be recorded every day. Format is hours (24 hour notation) and
                 minutes, e.g. 23:39.

`duration in seconds`: number of seconds to record the video for

### rpiSchedVideoListDaily

```
rpiSchedVideoListDaily
```

Lists the daily scheduled video captures. The format of the output is e.g.

```
22:52 16     -- /etc/cron.d/rpicam_22_52_16: 52 22 * * * raspberrypi . ~/rpi.source >>/home/raspberrypi/.rpi/rpi.log 2>&1 && rpiCaptureVideo $(rpiSchedVideoFileName 22_52_16) 16 >>/home/raspberrypi/.rpi/rpi.log 2>&1
23:03 16     -- /etc/cron.d/rpicam_23_03_16: 03 23 * * * raspberrypi . ~/rpi.source >>/home/raspberrypi/.rpi/rpi.log 2>&1 && rpiCaptureVideo $(rpiSchedVideoFileName 23_03_16) 16 >>/home/raspberrypi/.rpi/rpi.log 2>&1
```

### rpiSchedVideoRemoveDaily

```
rpiSchedVideoRemoveDaily <time as HH:MM> <duration in seconds>
```

Remove one scheduled daily video specified by the same parameters as were used to create it.
The values of the parameter are also presented when using the (#rpiSchedListDailyVideo) command.

`time as HH:MM`: time at which a video should be recorded every day. Format is hours (24 hour notation) and minutes, e.g. 23:39.

`duration in seconds`: number of seconds to record the video for


### rpiSchedVideoRemoveAllDaily

```
rpiSchedVideoRemoveAllDaily
```

Removes all the scheduled daliy videos

### rpiSchedVideoRetrieveRecordings

```
rpiSchedVideoRetrieveRecordings
```

Fetches the scheduled recordings from the Raspberry Pi device

### rpiSchedVideoDeleteRecordings

```
rpiSchedVideoDeleteRecordings
```

Delete all the scheduled recordings from the Raspberry Pi device

### rpiSchedVideoListRecordings

```
rpiSchedVideoListRecordings
```

List the scheduled recordings present on the Host and the Raspberry Pi device. For each file hst indicate that it is present on the host and rpi indicates that it is present on the Raspberry Pi devide.

```
$ rpiSchedVideoListRecordings
rpi_sched_video/22_52_15/rpicamvid_2025_08_13_22_52_15.mp4  hst rpi
rpi_sched_video/22_52_16/rpicamvid_2025_09_02_22_52_16.mp4  rpi
rpi_sched_video/22_52_16/rpicamvid_2025_09_03_22_52_16.mp4  rpi
rpi_sched_video/23_03_16/rpicamvid_2025_08_13_23_03_16.mp4  hst rpi
rpi_sched_video/23_03_16/rpicamvid_2025_09_02_23_03_16.mp4  rpi
rpi_sched_video/23_03_16/rpicamvid_2025_09_03_23_03_16.mp4  rpi
```

## Raspberry Pi functions

FIXME

[main-link]: ./README.md