# PS4 On Homebridge

These are instructions to create a series of buttons within the Apple Home app that control PS4 power and open/close any app you want.

1. Install Node.js

    Begin by installing `Node.js` if you haven't already. Their [website](https://nodejs.org/en/) has up-to-date versions and installation instructions for your OS.

    For macOS I use [Homebrew](https://brew.sh/) to install packages. If you're a developer (or an aspiring one), I highly recommend using it. Via homebrew, installing node is as simple as `brew install node`.

1. Install Homebridge

    `homebridge` is a node server that allows you to emulate a Homekit device from the comfort of your computer.

    We are going to install homebridge as a global node package. In your command line, run `sudo npm install -g --unsafe-perm homebridge`.

    Check the installation by running `homebridge`, and if everything loads as expected (you should see a big QR code), kill the server with `Control-C`.

1. Install PS4 Waker

    `ps4-waker` is a command line application that tricks your PS4 into turning on, going in to standby, opening an app, pressing buttons, etc. You can learn how it works by checking out its [documentation](https://github.com/dhleong/ps4-waker).

    Install it with `npm i ps4-waker -g`, then run it with `ps4-waker` and follow any instructions. It will ask you to install the PS4 Second Screen app on your iPhone, and enter a PIN from the PS4.

    1. Install CMD Switch

    `cmdswitch2` is a plugin for node that creates Homekit switches that can run shell commands.

    Install it with `npm install -g homebridge-cmdswitch2`.

1. Create a Config File

    Homebridge works by reading from a config file. Create a `config.json` in your `.homebridge` folder. It should be located at `~/.homebridge`.

    Paste the following config into it:

    ```json

    {
        "bridge": {
            "name": "Homebridge",
            "username": "CC:22:3D:E3:CE:30",
            "port": 51826,
            "pin": "031-45-154"
        },

        "platforms": [{
            "platform": "cmdSwitch2",
            "name": "CMD Switch",
            "switches": [
                {
                    "name" : "PS4",
                    "on_cmd": "ps4-waker",
                    "off_cmd": "ps4-waker standby",
                    "state_cmd": "ps4-waker search | grep -i '200 Ok'",
                    "polling": true,
                    "interval": 5,
                    "timeout": 2000,
                    "manufacturer": "Sony Corporation",
                    "model": "PS4",
                    "serial": "PS4"
                },
                {
                    "name" : "Destiny 2",
                    "on_cmd": "ps4-waker start CUSA05042",
                    "off_cmd": "ps4-waker remote ps",
                    "state_cmd": "ps4-waker search | grep -i 'CUSA05042'",
                    "polling": true,
                    "interval": 5,
                    "timeout": 2000,
                    "manufacturer": "Sony Corporation",
                    "model": "PS4",
                    "serial": "PS4"
                },
                {
                    "name" : "Netflix",
                    "on_cmd": "ps4-waker start CUSA00129",
                    "off_cmd": "ps4-waker remote ps",
                    "state_cmd": "ps4-waker search | grep -i 'CUSA00129'",
                    "polling": true,
                    "interval": 5,
                    "timeout": 2000,
                    "manufacturer": "Sony Corporation",
                    "model": "PS4",
                    "serial": "PS4"
                },
                {
                    "name" : "Hulu",
                    "on_cmd": "ps4-waker start CUSA00131",
                    "off_cmd": "ps4-waker remote ps",
                    "state_cmd": "ps4-waker search | grep -i 'CUSA00131'",
                    "polling": true,
                    "interval": 5,
                    "timeout": 2000,
                    "manufacturer": "Sony Corporation",
                    "model": "PS4",
                    "serial": "PS4"
                }
            ]
        }]
    }

    ```

    `on_cmd` is the command that will run when the Home switch goes to on. `off_cmd` runs when the switch goes off. And `state_cmd` runs periodically to determine what state the switch should be in. In this case, we search for a PS4 on the network, and then check to see if its running config contains either "200 Ok" (if we're checking to see if its on) or a Title ID (if we're checking to see if a specific app is running).

    Modify the json with whatever apps you want to open. You can find the Title ID of a specific app by running it on the PS4 and then running `ps4-waker search`. The ID should be in the response.

1. Start Homebridge

    Once your config is created, start Homebridge again with `homebridge`. It must remain running at all times to work.

1. Add the device to Home

    Open your Home app and add a new device with the plus button on the top right of the app. Scan the QR code at the next screen.

    All of your buttons should automatically be added to your Home!
