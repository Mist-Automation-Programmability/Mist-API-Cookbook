# Single Switch Upgrade
This will upgrade a single switch.

Required Variables:
apitoken
site_id
device_id

## Step 1:
Get a list of available switch firmware versions.  This API call will retrieve a list of available switch firmware versions that you can install via Mist.

By default, it will return only AP firmware to prevent breaking of the API for existing API consumers.  Adding query parameter of `?type=switch` will specify that you want a list of switch firmware.

```
GET
/api/v1/sites/:site_id/devices/versions?type=switch
```
RESPONSE:
```JSON
[
    {
        "version": "19.3R1.8",
        "_version": "19.3R1.8",
        "model": "EX4300-32F"
    },
    {
        "version": "18.3R3-S4.3",
        "_version": "18.3R3-S4.3",
        "model": "EX4300-32F"
    },
    {
        "version": "19.1R3-S4.6",
        "_version": "19.1R3-S4.6",
        "model": "EX4300-32F"
    },
    {
        "version": "18.4R2-S7.4",
        "_version": "18.4R2-S7.4",
        "model": "EX4300-32F"
    },
    {
        "version": "19.4R3-S1.3",
        "_version": "19.4R3-S1.3",
        "model": "EX4300-32F"
    },
...
```

## Step 2:
Perform upgrade on switch.  If you want the switch to reboot automatically, set the `reboot` key in the body.

```
 POST
 /api/v1/sites/:site_id/devices/:device_id/upgrade
```
BODY:
```JSON
{
    "version": "19.4R3-S1.3",
    "reboot": true
}
```

## Step 3: (Optional)
Trigger stat polling.  This enables the UI to update as the switch performs the upgrade steps

```
POST
/api/v1/sites/:site_id/devices/:device_id/poll_stats
```

#

# Multiple Switch Upgrades

## Step 1:
Get a list of available switch firmware versions.  Ensure that all models of switch have a specific version

```
GET
/api/v1/sites/:site_id/devices/versions?type=switch
```
RESPONSE:
```JSON
[
    {
        "version": "19.3R1.8",
        "_version": "19.3R1.8",
        "model": "EX4300-32F"
    },
    {
        "version": "18.3R3-S4.3",
        "_version": "18.3R3-S4.3",
        "model": "EX4300-32F"
    },
    {
        "version": "19.1R3-S4.6",
        "_version": "19.1R3-S4.6",
        "model": "EX4300-32F"
    },
    {
        "version": "18.4R2-S7.4",
        "_version": "18.4R2-S7.4",
        "model": "EX4300-32F"
    },
    {
        "version": "19.4R3-S1.3",
        "_version": "19.4R3-S1.3",
        "model": "EX4300-32F"
    },
...
```

## Step 2:
Perform upgrade on switches.  If you want the switch to reboot automatically, set the `reboot` key in the body.

```
POST /api/v1/sites/:site_id/devices/upgrade
```
```JSON
{
    "version":"20.3R1-S1.1",
    "reboot":true,
    "enable_p2p":false,
    "device_ids": ["00000000-0000-0000-1000-aabbccddeeff"]
}
```

## Step 3: (Optional)
Trigger stat polling.  This enables the UI to update as the switch performs the upgrade steps.  Perform for each switch in the list of being upgraded.

```
POST
/api/v1/sites/:site_id/devices/:device_id/poll_stats
```