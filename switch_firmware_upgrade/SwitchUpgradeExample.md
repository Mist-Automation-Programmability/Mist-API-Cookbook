# Single Switch Upgrade
This will upgrade a single switch.

### Required Variables:
* `apitoken`
* `site_id`
* `device_id`

## Step 1: Get list of available firmware
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
## Step 3: Verify Switch is connected
Before performing an upgrade on the switch, verify it is connected to the Mist cloud.

```
GET
/api/v1/sites/:site_id/stats/devices/:device_id
```

Response:
```JSON
{
    ...
    "status": "connected",
    ...
```

Alternatively, you can pull the stats for all switches at this site with the following:

```
GET
/api/v1/sites/:site_id/stats/devices?type=switch
```


## Step 3:
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

## Step 4: (Optional)
Trigger stat polling.  This enables the UI to update as the switch performs the upgrade steps

```
POST
/api/v1/sites/:site_id/devices/:device_id/poll_stats
```

#

# Multiple Switch Upgrades
The idea is to perform multiple switch upgrades as the same time. You must upgrade switches per site, so aggregating all the switches into a list per site_id is ideal.

### Required Variables:
* `apitoken`
* `org_id` (optional)
* `site_id`  for each site where you have devices you want to upgrade.
* `device_id` for each device you want to upgrade at a site.


## Step 0: (Optional)
If you don't have a list of devices and sites, my preferred method is to pull the org inventory.  Each switch will have it's `id` (device_id), and `site_id.

```
GET
/api/v1/orgs/:org_id/inventory?type=switch
```

Response:
```JSON
[
    {
        "mac": "00000000006c",
        "serial": "JY0000000005",
        "model": "EX2300-24P",
        "sku": "EX2300-24P",
        "hw_rev": "03",
        "type": "switch",
        "magic": "ZA000000000000N",
        "name": "TestSwitch1",
        "org_id": "xxxxxxx6-xxxx-xxxx-xxxx-xxxxxxxxxxx6",
        "site_id": "xxxxxxx8-xxxx-xxxx-xxxx-xxxxxxxxxx05",
        "created_time": 1594759206,
        "modified_time": 1613669091,
        "id": "00000000-0000-0000-1000-00000000006c",
        "deviceprofile_id": null,
        "connected": true
    },
    {
        "mac": "0000000000ee",
        "serial": "JY000000009",
        "model": "EX2300-24P",
        "sku": "EX2300-24P",
        "hw_rev": "M",
        "type": "switch",
        "magic": "59000000000",
        "name": "TestSwitch2",
        "org_id": "xxxxxxx6-xxxx-xxxx-xxxx-xxxxxxxxxxx6",
        "site_id": "xxxxxxx2-xxxx-xxxx-xxxx-xxxxxxxxxx03",
        "created_time": 1595281234,
        "modified_time": 1612912555,
        "id": "00000000-0000-0000-1000-0000000000ee",
        "deviceprofile_id": null,
        "connected": true
    }
]
```


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
Perform upgrade on switches at a single site.  Iterate through all sites that have switches you want to upgrade.  If you want the switch to reboot automatically, set the `reboot` key in the body.

```
POST /api/v1/sites/:site_id/devices/upgrade
```
```JSON
{
    "version":"20.3R1-S1.1",
    "reboot":true,
    "enable_p2p":false,
    "device_ids": ["00000000-0000-0000-1000-00000000006c"]
}
```

## Step 3: (Optional)
Trigger stat polling.  This enables the UI to update as the switch performs the upgrade steps.  Perform for each switch in the list of being upgraded.

```
POST
/api/v1/sites/:site_id/devices/:device_id/poll_stats
```