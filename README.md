# pyPS4Controller
[![PyPI version shields.io](https://img.shields.io/pypi/v/pyPS4Controller.svg)](https://pypi.python.org/pypi/pyPS4Controller/) 
[![PyPI pyversions](https://img.shields.io/pypi/pyversions/pyPS4Controller.svg)](https://pypi.python.org/pypi/pyPS4Controller/)
[![Downloads](https://pepy.tech/badge/pyPS4Controller)](https://pepy.tech/project/pyPS4Controller)
##

![PS4 Controller](https://github.com/ArturSpirin/pyPS4Controller/blob/master/assets/ds4.jpg)

pyPS4Controller is a light module designed to provide hooks for PS4 Controller using Python on Linux.

## Installation
`sudo pip install pyPS4Controller`

## Usage

1. Connect your controller to Bluetooth. Either via GUI, bluetoothctl, or ds4drv (_Not recommended, see why at the bottom_).
2. Check which interfaces you have available at `/dev/input` via `ls -la /dev/input` command. 
You are looking for `jsN` interfaces. When you connect a new device, there should be a new file created and its 
typically starts at `js0`. The more controllers/joysticks are connected the more `jsN` interfaces you will see. 
So if you have only one controller/joystick connected, you should only see 1 interface: `/dev/input/js0`, 
take a note of it as we will need it in next step. _If you have multiple controllers and not sure which interface 
belongs to which controller, you can disconnect it see which interface is no longer available._
3. Copy the code bellow into a Python file, lets say `test.py`
    ```python
    from pyPS4Controller.controller import Controller
    
    
    class MyController(Controller):
    
        def __init__(self, **kwargs):
            Controller.__init__(self, **kwargs)
    
    
    controller = MyController(interface="/dev/input/js0", connecting_using_ds4drv=False)
    controller.listen()
    ```
4. Now run that file like so `python test.py` and use your controller. 
You will see output on your screen based on the input to your controller. 
You can bind your own logic to each one of those events. 
Lets say you want print "Hello world" on X press and "Goodbye world" on X release then the code would look like this:
    ```python
    from pyPS4Controller.controller import Controller
        
        
    class MyController(Controller):
    
        def __init__(self, **kwargs):
            Controller.__init__(self, **kwargs)
    
        def on_x_press(self):
           print("Hello world")

        def on_x_release(self):
           print("Goodbye world")
   
    controller = MyController(interface="/dev/input/js0", connecting_using_ds4drv=False)
    controller.listen()
    ```
   
Here is a list of all the events you can override in a similar manner:
```
on_x_press
on_x_release
on_triangle_press
on_triangle_release
on_circle_press
on_circle_release
on_square_press
on_square_release
on_L1_press
on_L1_release
on_L2_press
on_L2_release
on_R1_press
on_R1_release
on_R2_press
on_R2_release
on_up_arrow_press
on_up_down_arrow_release
on_down_arrow_press
on_left_arrow_press
on_left_right_arrow_release
on_right_arrow_press
on_L3_up
on_L3_down
on_L3_left
on_L3_right
on_L3_at_rest
on_L3_press
on_L3_release
on_R3_up
on_R3_down
on_R3_left
on_R3_right
on_R3_at_rest
on_R3_press
on_R3_release
on_options_press
on_options_release
on_share_press
on_share_release
on_playstation_button_press
on_playstation_button_release
```

# Why ds4drv is not recommended?

This module was originally written to work with ds4drv but I have experienced a number of problems like:
- Random events for R3 joystick and some button presses were coming through when controller was not even used and 
they would also be generated when controller was in use. This was the biggest issue I could not get over.
- My controller would frequently disconnect from ds4drv.
- Pairing it back in rare cases took many retries.
- Not all events are sent to the interface. If you decide to connect through ds4drv those button events are not supported:
  - L3 & R3 Clicks/Releases (Joysticks work, its just the click on the top of the joystick is not detected)
  - Events on the Playstation button is not detected
  - Share button events are not detected
  
If you still want to connect using ds4drv here is a [video tutorial](https://www.youtube.com/watch?v=CeyGP3_kKZI) on 
how to do it or you can follow the steps bellow:

1. First, you have to make sure you can connect your PS4 controller with ds4drv. You can do it 1 of 2 ways and you only need to do this once.
   * Manually by following [this instructions](https://github.com/macunixs/dualshock4-pi)
   * Automatically by running `py3ps4c init` (if you are using python3) or `py2ps4c init` (if you are using python2) in your terminal.
   
2. (Optional) connect your controller directly to the computer's Bluetooth module. 
       Once successful, disconnect the controller. If you don't do this, in the next step you may see this error: 
       `Unable to connect to detected device: Failed to set operational mode: [Errno 107] Transport endpoint is not connected`
3. Now start `sudo ds4drv` and press `SHARE` + `PS4` button on your controller. If pairing fails, you want
to try in again, it should eventually connect.

## Known limitations at this time
* Mouse pad events and clicks are not detected
