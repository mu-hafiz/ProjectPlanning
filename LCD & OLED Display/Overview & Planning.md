## Main Idea

The main idea is to take my existing bedroom controller and convert it so that it has an OLED display that can be used as a display menu. The menu system will be navigated using a potentiometer/rotary encoder, and will have buttons to confirm and go back from the menus

---
## Menu System

The exact things to be used in the menu aren't set in stone yet, but some ideas are:
- Toggling the brightness/colour of the main light
- Set time for how long heater is on for & display time left
- LED Presets?

I can always think of more later, get it up and running first.

---
## Multiple Pages

```
display:
  - platform: lcd_pcf8574
    id: my_lcd
    ...
    lambda: |-
      id(my_lcd_menu).draw(); <--- Fig. 1
      if (!id(my_lcd_menu).is_active()) <--- Fig. 2
        it.print("Menu is not active");

lcd_menu:
  id: my_lcd_menu
  display_id: my_lcd
  active: true
  mode: rotary
```

This is a snippet taken from the [Display Menu](https://esphome.io/components/display_menu/index.html#overview) page in the overview section.
As you can see in Fig. 1, we draw the lcd_menu onto the display, and so, it is possible to choose whether to draw the menu interface or whether to draw another page.
In Fig. 2, we can see logic based on the displayed page, and so there is a way to choose which page to display.

```
  # Templated
  - display.page.show: !lambda |-
      if (id(my_binary_sensor).state) {
        return id(page1);
      } else {
        return id(page2);
      }
```

^4097bf

This next snippet is taken from the [Display Component](https://esphome.io/components/display/index.html?highlight=display+pages#display-pages) page, discussing how multiple pages can be used. We can see that we can choose which page to display based on the state of a sensor, and so, we should be able to use some logic to choose what to display based on factors such as if the electric blanket is on, and so forth.

One example is to make the screen show that the electric heater is on, and when a button is pressed or the dial is moved, is activates a boolean, and after some time, that boolean deactivates again, acting as a "active" sign. Then, when displaying the page, we can see whether this boolean is on or not, if it is on, then we can display the menu system, if it isn't on, then we can display the other pages based on the other factors.

---
## Getting Sensors from Home Assistant

In order to get sensors from Home Assistant (for example, to check whether the electric blanket is on or off), we use this following snippet of code from this [page](https://esphome.io/components/sensor/homeassistant):

```
sensor:
  - platform: homeassistant
    name: "Temperature Sensor From Home Assistant"
    entity_id: sensor.temperature_sensor
    id: temp_sensor  <---- I added this for context
```

We create a new sensor on the ESP board with ESPHome installed onto it, and we tell it the ID of the entity that we want the sensor to track.
We can also specify it's own ID (which I did on the bottom) so that we can reference it when doing the templating, which is crucial when deciding [[#^4097bf|which page to display]]. You can see that we check the state of the sensor with ID "my_binary_sensor", and so, we can replace this with any sensor we want.

---
## Night Time

I need to remember to ensure that when my phone goes on charge (when I go to sleep), the display is turned off, so that it doesn't keep me up.

---
## After Functionality

I then need to 3D Print a new enclosure for everything and design how it will all fit.