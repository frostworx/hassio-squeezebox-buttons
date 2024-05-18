# hassio-squeezebox-buttons
Send all your squeezebox buttons to Home Assistant via webhook

WIP - upcoming:

Some scripts which send all button presses on a Logitech Squeezebox
to your Home Assistant via webhook _(only tested with Squeezebox Radio)_

No dependencies, just install them via ssh and add them to your init scripts.

On Home Assistant side, just add a webhook for each of your Squeezeboxes and an additional automation for all buttons.

Heavily unpolished, feel free to send PRs or _(even better)_
write a proper hassio-addon with a better user-experience.

_(in the end we could even get rid of the Logitech Media Server (or Slim Proto) part and turn the Squeezebox into a fully fledged Home Assistant Client including GUI)_

TODO: everything - the local scripts do work are not ready yet for a commit :D
