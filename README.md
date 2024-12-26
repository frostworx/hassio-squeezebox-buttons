# hassio-squeezebox-buttons

Send all your squeezebox buttons to Home Assistant via webhook

Short script which send all button presses on a Logitech Squeezebox
to your Home Assistant via webhook _(only tested with Squeezebox Radio)_

Trigger whatever action you like for each of them using a Home Assistant Automation.

Additionally send data of the Squeezebox lightsensor to Home Assistant via 2nd webhook.

No dependencies, just scp it to your Squeezebox and add it to the init scripts.

The only linux tools used are already preinstalled in the squeezebox

- hexdump to read the raw input event
- wget to send the curresponding snippet to the home assistant webhook api

On Home Assistant side, just add a webhook for each of your Squeezeboxes and an additional automation for all buttons.

Heavily unpolished, feel free to send PRs or _(even better)_ write a proper hassio-addon with a better user-experience.

_(the buttons are completely independant from LMS - in theory we could even get rid of the Logitech Media Server (or Slim Proto) part and turn the Squeezebox into a fully fledged Home Assistant Client including GUI)_


# Installation

copy the script to your squeezebox _(using ssh)_

example:

 `scp -O sbevmon yoursqueezebox:/usr/bin/`

_(default squeezebox root password is '1234'')_

make it executable:
 `chmod +x /usr/bin/sbevmon`

## auto config

By default 'sbevmon' uses

- the md5sum of the squeezebox mac adress as webhook for the button presses
- the md5sum of that md5sum as webhook for the light sensor
- the LMS server as home assistant ip

to print all of them, simply call `sbevmon` with any argument.

If that works for you, skip to [#in-the-squeezebox], else read on

## manual config

you can also configure webhooks and the home assistant ip manually:
comment in the

```
#BWH="87161aac-f0ae-4939-8ad3-4f36b6482bb9"  # button webhook
#LWH="-F45c1sMSuxX-P34CE0uT-fwx"             # light webhook
#HA="homeassistant.local"                    # home assistant ip/hostname
```

at the top and adjust them to your needs:

- replace the example webhook in sbevmon _(and the example home assistant automation)_ with your own one - for example using sed:

`sed "s:87161aac-f0ae-4939-8ad3-4f36b6482bb9:7ca6e1d5-0343-42fa-a707-545c76e1b4df:g" -i sbevmon`

`sed "s:87161aac-f0ae-4939-8ad3-4f36b6482bb9:7ca6e1d5-0343-42fa-a707-545c76e1b4df:g" -i mysb-send-button`

optionally replace `homeassistant.local` with your home assistant dns or ip in `sbevmon`

example:

`sed "s:homeassistant.local:10.10.3.123:g" -i sbevmon`

You can easily generate a custom uuid on [duckduckgo](https://duckduckgo.com/?q=uuid&ia=answer) _(found in [this excellent howto](https://community.home-assistant.io/t/sensor-for-computer-usage/59522/20))_
Alternatively create a new automation in home assistant and select a webhook as trigger. then enter the autocreated webhooks in `sbevmon`

## in the squeezebox

- ssh into your squeezebox

- add the script to `/etc/init.d/rcS.local` _(does not exist by default)_
_(commented for a reason, you might have already a custom rcS.local)_:

#echo "/usr/bin/sbevmon &" > /etc/init.d/rcS.local
#echo "exit 0" >> /etc/init.d/rcS.local

example _(ymmv)_:


`
# cat /etc/init.d/rcS.local
/usr/bin/sbevmon &
exit 0
`

- make the scripts and the init script executable

`chmod +x /usr/bin/sbevmon`

`chmod +x /etc/init.d/rcS.local`

- reboot - `sbevmon` should automatically start and run in the background
- _(ssh back in and check with 'ps aux | grep sbevmon' if you want)_

# home assistant:

add a new automation for the squeezebox buttons
You might want to use the provided 'mysb-send-button'
_(don't forget to adjust the webhook_id to your uuid used avove)_
and adjust it to your needs

add a new automation for the squeezebox light sensor using your webhook


*Done*  - every button pressed should appear in the home assistant messages separately _(tested with 3 Squeezeboxes)_

Adjust every action to your _(more useful)_ needs.
Might make sense to trigger different actions, depending on the current media_player state.
Would be glad to read of your ideas.

Currently I do not plan do actively enhance the current state, but if you're interested in making something bigger out of it, I'd be glad to help.
