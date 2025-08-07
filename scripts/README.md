# User guide

FIXME: empty for now

# Reference

## rpiSetup

```
rpiSetup <rpi ip address> <username>
```

This bash function sets-up the information needed to connect to the Raspberry pi device and makes it possible to access it without repeating these information on every access.
Calling this is necessary to be able to use the other functions described here.
Note that there is currently only support for one such device.

The information about the Raspberry Pi device are stored inside the .rpi directory in the home of the user who calls rpiSetup.

`rpi ip address`: IP address of the Raspberry Pi device to communicate with.

`username`: The name of the user to use on the Raspberry Pi device.

## rpiGetAccess

```
rpiGetAccess
```

This bash function prints on stdout the access string <username>@<ip address> for the Raspberry Pi device that has been setup using [rpiSetup](#rpiSetup). It is mainly thought of as an internal function.

## rpissh

```
rpissh [<command>]
```

This bash function is used to ssh to the Raspberry Pi that has been setup using [rpiSetup](#rpiSetup).

`command`: optional command to execute on the Raspberry Pi device. If command is passed, then the command is executed on the Raspberry Pi device (make sure to use quotes and escape elements in the command as needed). If command is omitted, a shell will be created on the Raspberry Pi device allowing interaction with it.

## rpirsyncTo

```
rpirsyncTo <path>
```

This bash function rsyncs from the machine that the function is run on and to the Raspberry Pi setup using [rpiSetup](#rpiSetup).

`path`: path that should be rsynce'ed.

## rpirsyncFrom 

```
rpirsyncFrom <path>
```

This bash function rsyncs from the Raspberry Pi setup using [rpiSetup](#rpiSetup) to the machine that the function is run on.

`path`: path that should be rsynce'ed.

## rpiCaptureStill

```
rpiCaptureStill <file>
```

This bash function takes a photo on the Raspberry Pi setup using [rpiSetup](#rpiSetup) and transfers the photo back to the machine running the function.

`file`: name of the file that the photo should be saved as.

## rpiCaptureLive

```
rpiCaptureLive <file> <time in seconds>
```

This bash function makes a video on the Raspberry Pi setup using [rpiSetup](#rpiSetup) and transfers the video back to the machine running the function.

`file`: name of the file that the video should be saved as.

`time in seconds`: number of seconds that the video should be recorded for.
