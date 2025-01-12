Use your validator/Node to send information to your webhost to update your toml file, then create a landing page based on the data found in your toml file.

No connection (no holes) to your validator or node,

update every prompt from ledger activity (via key ledger by default) via the listener.py module

or in timed intervals, by setting 'load-type' within in the update.py file to standalone, and running the update.py direct (instead of listener.py)

Be more transparent. Display your validator info, load, amendments, organization and principle. all Hands free, and automated

Works on xrpl mainnet, xahau, testnet

Xahau example here: https://xahau.validator.report/ or https://xahau.zerp.network

Mainnet example here: https://mainnet.validator.report/ and here: https://mainnet2.validator.report/


# Setup Instructions

# Part 1: Validator/Node Server Setup

Prerequisites:

free, df, awk, pip, sysstat

    sudo apt install pip sysstat

Python3 requires: requests

    sudo pip3 install requests toml

Optional text editor: nano

If you want to use this JUST for you Node, then you can do that by JUST using the updater.py file. and running python3 update.py
Upload Pre-existing Files: Upload update.py and listener.py to the validator server or use nano to create these files and paste the contents accordingly, or you can git clone.

### Editing update.py:
Modify the following lines:

 - `xrpl` = 'xahaud' # Replace with your XRPL node executable eg. "rippled" or "xahaud"
 - `load_type` = 'standalone' # option of 'standalone' or 'listener', when in standalone it uses a built in timer to trigger the update, and 'listener' will do do one update a stop.
 - `mode` = 'node' # option of 'node' or 'validator', when using validator type, it checks/logs the AMENDMENTS, and saves toml via API, 'node' has no amendments and saves locally
 - `wait_time` = 900 # wait time before re-creating .toml (in seconds)
 - `data_point_amount` = 6 # amount of data points to retain in .toml file, (useful for limiting graph data to a day)
 - `api_url` = 'https://yourhost.com/toml.php'  # Replace with your API URL (when in validator mode)
 - `api_key` = 'key'  # Replace with your API key, this can be anything you want, you need to update the php script to match
 - `node_config_path` = '/opt/xahaud/etc/xahaud.cfg' # path to node .cfg file, for use in data gathering
 - `file_path` = '/home/www/.well-known/xahau.toml' # path to local .toml file (for use in node mode)
 - `allowlist_path` = '/root/xahl-node/nginx_allowlist.conf' # allow list path, for use in connections output (node mode)
 - `websocket_port` = '6008' # port thats used for websocket (for use in connections, in node mode)

### Editing listener.py for use in `load_type = 'listener' ``:
Modify the line if necessary:
    uri = "ws://127.0.0.1:6009": Replace with the correct WebSocket server URI, you can find this in your validator config "port_ws_admin_local"


# Part 2: Web Host Setup

Webhost requires:

### PHP

Editing toml.php, Change the following lines:

    $allowedIPAddress = '0.0.0.0': Replace with your validator IP address to reject other sources
    $apiKey = 'key': Set your API key (must match the one in update.py)
    $filePath = '.well-known/xahau.toml': Change the file path as needed (xrp-ledger.toml for Mainnet)

then Set file permissions to 644


### Editing index.html:

Replace .well-known/xahau.toml with the correct TOML file path (use xrp-ledger.toml for xrpl Mainnet)


# PART 3, Starting the Script, 

To run the script you have many options

- if you want to trigger update every ledger close, then have `load_mode = 'listener'` in the update.py setting, and do `nohup python3 listener.py &` this then updates the .toml file depending on the ledger action

- if you want to update the .toml file at regular intervals, set `load_mode = 'standalone'`, and adjust `wait_time = 60` in updater.py file, and then do `nohup python3 update.py`

- or another method, have `load_mode = 'listener'` set in update.py file, and then setup a cronjob with the time frame you want,
 for example a entry of `*/15 * * * * /usr/bin/python3 /root/xahl-node/updater.py` would run updater every 15 minutes.

- or manually by a simple `python3 update.py` (make sure file has correct run permission `chmod +x update.py`)

### Stopping the Script:

Find the process ID with ps aux | grep python

Terminate using kill [process id]
