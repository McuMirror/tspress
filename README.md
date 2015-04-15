## tspress

A Qt app for tracking mouse/touchscreen pointer movements.

Used to evaluate a BeagleBone Black touchscreen.

Assuming Qt5 dev libraries and headers are already installed.

### Build natively

    $ qmake && make

### Operation

The UI places 5 buttons along the top of the screen.

You can test with a real USB mouse connected or by using the
touchscreen directly.
    
### Run under X11

Make sure you have calibrated the screen with *xinput_calibrator* first.

    $ export DISPLAY=:0.0
    $ xinput_calibrator

Then run the app

    $ export DISPLAY=:0.0 (if not already set)
    $ ./tspress


### Run without X

    $ ./tspress -platform linuxfb -plugin evdevtouch:/dev/input/touchscreen

That assumes that a */dev/input/touchscreen* exists. You may need to create
it or just point evdevtouch at the correct */dev/input* device.

Here's an example from a BBB system using an NH5Cape touchscreen

    # dmesg | grep input
    [    1.950352] input: tps65217_pwr_but as /devices/ocp.3/44e0b000.i2c/i2c-0/0-0024/input/input0
    [    7.692970] input: gpio_keys.7 as /devices/gpio_keys.7/input/input1
    [    8.300152] input: ft5x0x_ts as /devices/virtual/input/input2

I know the driver is `ft5x0x_ts`.

    # ls -l /dev/input
    total 0
    drwxr-xr-x 2 root root      80 Apr 15 13:56 by-path
    crw-rw---- 1 root input 13, 64 Apr 15 13:56 event0
    crw-rw---- 1 root input 13, 65 Apr 15 13:56 event1
    crw-rw-rw- 1 root input 13, 66 Apr 15 13:56 event2
    crw-rw---- 1 root input 13, 63 Apr 15 13:56 mice
    crw-rw---- 1 root input 13, 32 Apr 15 13:56 mouse0
    lrwxrwxrwx 1 root root       6 Apr 15 14:55 touchscreen -> event2

That's after running

    # chmod o+rw /dev/input/event2
    # ln -s /dev/input/event2 /dev/input/touchscreen

The *touchscreen* link is not necessary. The permissions change 
or adding the user to the *input* group is necessary.

A simple test to see if you got the write `/dev/input` device is to
run the following then move your finger around the screen.

    $ cat /dev/input/input2 | hexdump
    0000000 8df8 552e ce89 0003 0003 0030 00c8 0000
    0000010 8df8 552e ce89 0003 0003 0035 01b4 0000
    0000020 8df8 552e ce89 0003 0003 0036 0150 0000
    0000030 8df8 552e ce89 0003 0003 0032 0001 0000
    ...
    0000730 8df8 552e 2bd6 0007 0003 0030 0000 0000
    0000740 8df8 552e 2bd6 0007 0000 0002 0000 0000
    0000750 8df8 552e 2bd6 0007 0001 014a 0000 0000
    0000760 8df8 552e 2bd6 0007 0000 0000 0000 0000
    ^C

