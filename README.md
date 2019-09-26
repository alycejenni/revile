This is a simple example package for slit scanning using a (fairly new) Canon DSLR and a 28BYJ-48 stepper motor.

## Requirements

### Equipment
1. Arduino Uno
  - 28BYJ-48 stepper motor (with secure flat surface attached to act as a turntable)
  - ULN2003 driver
  - USB cable
2. Camera (some parts will only work with newer Canon DSLRs, others can use anything connected as a webcam)
  - USB cable
3. Computer with access to a terminal/command line (only tested on Linux but may work on others)

### Software
1. [python 3.6+](https://www.python.org)
2. [pip](https://pypi.org/project/pip)
3. [gphoto2](https://github.com/gphoto/gphoto2)
4. [sox](http://sox.sourceforge.net) (optional - for playing a beep to signal end of turntable movement)
5. [v4l2loopback](https://github.com/umlaeute/v4l2loopback) (optional - for using cameras other than new Canon DSLRs)
6. [ffmpeg](https://ffmpeg.org) (optional - for using cameras other than new Canon DSLRs)


## Setup

### Camera setup
1. plug in via USB
2. turn on
3. point at the specimen on the motor turntable
4. orient _vertically_/in _portrait_ mode (90 degrees clockwise)
5. focus on the subject

**For Canon cameras:**
1. insert an SD card, preferably a high-speed one
2. set to video mode
3. configure the desired video settings on the camera (e.g. 60fps vs 30fps)

### Motor turntable
1. plug in to the driver and the Arduino
2. plug the Arduino in via USB
3. fix the subject securely onto the turntable

### Software
Install using pip:

```sh
pip install git+git://github.com/alycejenni/revile.git#egg=revile
```

**For cameras other than new Canon DSLRs:**
The camera needs to be streaming from a /dev/video device. For a camera connected via USB with a preview mode available, you can do this using `v4l2loopback`. After installing `v4l2loopback`:
```sh
modprobe v4l2loopback
sudo find /dev -name 'video*' | sort | tail -n 1  # to find the device
gphoto2 --stdout --capture-movie | ffmpeg -i - -vcodec rawvideo -pix_fmt yuv420p -threads 8 -r 60 -f v4l2 /dev/video[DEVICE NUMBER HERE]
```

## Usage

### Canon mode
```sh
revile video --length $SECONDS --outputdir $OUTPUTDIR
```

e.g. to create and process a 20s video, then save it in `/data/videos`:
```sh
revile video --length 20 --outputdir /data/videos
```

### Webcam mode
```sh
revile stream --frames $FRAMES --stream-port $DEVICE
```

e.g. to create and process 500 frames using device `/dev/video1`:
```sh
revile stream --frames 500 --stream-port 1
```
