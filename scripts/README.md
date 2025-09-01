# User guide

## Getting started

### pre-requisites

In order to be able to use this set of bash functions, you need:

* a Raspberry Pi device turned on and connected to the internet
* a camera module attached to the Raspberry Pi device
* the IP address of the Raspberry Pi device
* the username and the password for one user on the Raspberry Pi device
* a host machine able to run bash (e.g. using terminal on a Mac OS X or linux, using Putty on Windows)

### Making my first photo

First of all, you need to clone this repo onto your host machine:

```
$ cd my_rpi_dir && git@github.com:knudhansen/rpi.git
```

Then you need to source the file defining all the function of this repo:

```
$ source scripts/rpi.source
```

Now you need to specify the Raspberry Pi device you want to use. This is done by calling the rpiSetup function

```
$ rpiSetup 192.168.0.77 raspberrypi
raspberrypi@192.168.0.7's password:
```

This will create a `.rpi` directory under the home directory of your user on the host machine and save the information about the Raspberry Pi device in it.
It will also write the authentication public key onto the Raspberry Pi device. This requires typing the password for the chosen user on the Raspberry Pi device.

```
$ rpiCapturePhoto my_first_photo.jpeg
```

This function will have the Raspberry Pi device take a photo and will then fetch the photo back onto the host machine.

```
$ ls -al my_first_photo.jpg
-rw-r--r--@ 1 myuser  mygroup  1131714 11 Aug 11:59 my_first_photo.jpg
```

### Scheduling a daily video

Now that we have taken our first photo, we know that our Raspberry Pi is connected correctly to our host machine, and we now can try to setup daily video recording.

First let's check that we do not have any scheduled daily videos:

```
$ rpiSchedVideoListDaily
$ rpiSchedVideoListRecordings
```

Both of these functions should return with no output.

Now let's schedule a 56 seconds video recording every day at 12:34.

```
$ rpiSchedVideoAddDaily 12:34 56
```

If we look at the list of scheduled videos, we now should see the created schedule:

```
$ rpiSchedVideoListDaily
12:34 56     -- /etc/cron.d/rpicam_12_34_56: 34 12 * * * raspberrypi . ~/rpi.source >>/home/raspberrypi/.rpi/rpi.log 2>&1 && rpiCaptureVideo $(rpiSchedVideoFileName 56) 56 >>/home/raspberrypi/.rpi/rpi.log 2>&1
$ rpiSchedVideoListRecordings
```

It is not 12:34 yet, so There are still no recordings on the Raspberry Pi device, why rpiSchedVideoListRecordings outputs nothing. Once it is 12:35 (the recording finishes around 12:34:56), we can check that the recording has been made and is present on the Raspberry Pi device:

```
$ rpiSchedVideoListRecordings
rpicamvid_2025_08_11_12_34_56.mp4
```

We can then retrieve the recording to the host machine:

```
$ rpiSchedVideoRetrieveRecordings
receiving file list ... done
./
12_34_56/rpicamvid_2025_08_11_12_34_56.mp4

sent 44 bytes  received 905626 bytes  603780.00 bytes/sec
total size is 905219  speedup is 1.00
```

They are saved on the host machine under the rpi_sched_video directory

```
$ find rpi_sched_video/ -type f
rpi_sched_video/12_34_56/rpicamvid_2025_08_11_12_34_56.mp4
```

# RPI Functions Reference

Please find the reference for the provided bash functions [here][link-functions-reference-readme-page]

# RPI Implementation Documentation

Please find the documentation for how the RPI implementation [here][link-implementation-details-readme-page]

# Improvement list

Please find the list of ideas for possible improvements [here][link-improvements-readme-page].

[link-functions-reference-readme-page]: ./README_FUNCTIONS_REFERENCE.md
[link-implementation-details-readme-page]: ./README_IMPLEMENTATION_DETAILS.md
[link-improvements-readme-page]: ./README_IMPROVEMENTS.md
