[![](https://img.shields.io/github/release/ollo69/ha-samsungtv-smart/all.svg?style=for-the-badge)](https://github.com/ollo69/ha-samsungtv-smart/releases)
[![hacs_badge](https://img.shields.io/badge/HACS-Custom-orange.svg?style=for-the-badge)](https://github.com/custom-components/hacs)
[![](https://img.shields.io/github/license/ollo69/ha-samsungtv-smart?style=for-the-badge)](LICENSE)
[![](https://img.shields.io/badge/MAINTAINER-%40ollo69-red?style=for-the-badge)](https://github.com/ollo69)
[![](https://img.shields.io/badge/COMMUNITY-FORUM-success?style=for-the-badge)](https://community.home-assistant.io)

# HomeAssistant - SamsungTV Smart Component

This is a custom component to allow control of SamsungTV devices in [HomeAssistant](https://home-assistant.io). Is a modified version of the built-in [samsungtv](https://www.home-assistant.io/integrations/samsungtv/) with some extra features.<br/>
**This plugin is only for 2016+ TVs model!** (maybe all tizen family)

This project is a fork of the component [SamsungTV Tizen](https://github.com/jaruba/ha-samsungtv-tizen). I added some feature like the possibility to configure it using the HA user interface, simplifing the configuration process. I also added some code optimizition in the comunication layer using async aiohttp instead of request.
</br>
**Most part of the code and documentation available here come from the original project.**
</br>

**Development for this project relies solely on donations, so if you enjoy this component, please consider [becoming a patron](https://www.patreon.com/powder_tech) or [donating](https://powder.media/donate) to ensure it's continued survival.**

# Additional Features:

* Ability to send keys using a native Home Assistant service
* Ability to send chained key commands using a native Home Assistant service
* Supports Assistant commands (Google Home, should work with Alexa too, but untested)
* Extended volume control
* Ability to customize source list at media player dropdown list
* Cast video URLs to Samsung TV
* Connect to SmartThings Cloud API for additional features: see TV channel names, see which HDMI source is selected, more key codes to change input source

![N|Solid](https://i.imgur.com/8mCGZoO.png)
![N|Solid](https://i.imgur.com/t3e4bJB.png)

# Installation

### 1. Easy Mode

Install via HACS.

### 2. Manual

Install it as you would do with any homeassistant custom component:

1. Download `custom_components` folder.
1. Copy the `samsungtv_smart` directory within the `custom_components` directory of your homeassistant installation. The `custom_components` directory resides within your homeassistant configuration directory.
**Note**: if the custom_components directory does not exist, you need to create it.
After a correct installation, your configuration directory should look like the following.
    ```
    └── ...
    └── configuration.yaml
    └── custom_components
        └── samsungtv_smart
            └── __init__.py
            └── media_player.py
            └── websockets.py
            └── shortcuts.py
            └── smartthings.py
            └── upnp.py
            └── exceptions.py
            └── ...
    ```

# Configuration

Once the component has been installed, you need to configure it in order to make it work.
There are two ways of doing so:
- Using the web interface (Lovelace) [**recommended**]
- Manually editing the `configuration.yaml` file

**Important**: To complete the configuration procedure properly, you must be sure that your TV is turned on and connected to the LAN. Stay near to your TV during configuration because probably you will need to accept the access request that will prompt on your TV screen.

**Note**: To configure the component for using SmartThings (strongly suggested) use this [guide](https://github.com/ollo69/ha-samsungtv-smart/blob/master/Smartthings.md).

### Option A: Configuration using the web UI [**recommended**]

1. From the Home Assistant front-end, navigate to 'Configuration' then 'Integrations'. Under 'Set up a new integration' locate     '**SamsungTV Smart**' and click 'Configure'.
2. In the configuration mask, enter the IP address of the TV, the name for the entity and click 'Submit'
3. **Important**: look for your TV screen and confirm **immediatly** with OK if a popup appear.
4. Congrats! You're all set!

### Option B: Configuration via editing `configuration.yaml`

1. Enable the component by editing the configuration.yaml file (within the config directory as well). Edit it by adding the following lines:
    ```
    samsungtv_smart:
      - host: <YOUR TV IP ADDRES>
        name: My TV name
        ...
    ```
2. Restart Home Assistant.
3. **Important**: look for your TV screen and confirm **immediatly** with OK if a popup appear.
4. Congrats! You're all set!

### For best performance you should now move to the next section to create a custom `app_list`.

# Custom configuration parameters

You can configure additional option for the component using configuration variable in `configuration.yaml` section.<br/>
Some of this option are available only during component configuration because are stored in the registry during setup phase, other can be changed in `configuration.yaml` at any moment.<br/>

Section in `configuration.yaml` file must always start with `host` as shown in the following example:<br/>
```
samsungtv_smart:
  - host: <YOUR TV IP ADDRES>
    ...
```
Then you can add any of the following parameters:<br/>

- **api_key:**<br/>
(string)(Optional)<br/>
API Key for the SmartThings Cloud API, this is optional but adds better state handling on, off, channel name, hdmi source, and a few new keys: `ST_TV`, `ST_HDMI1`, `ST_HDMI2`, `ST_HDMI3`, etc. (see more at [SmartThings Keys](https://github.com/ollo69/ha-samsungtv-smart/blob/master/Smartthings.md#smartthings-keys))<br/>
Read [How to get an API Key for SmartThings](https://github.com/ollo69/ha-samsungtv-smart/blob/master/Smartthings.md)<br/>
This parameter can also be provided during the component configuration using the user interface.<br/>
**Note: this parameter is used only during initial configuration and then stored in the registry. It's not possible to change the value after that the component is configured. To change the value you must before delete the integration from UI.**<br/>

- **device_name:**<br/>
(string)(Optional)<br/>
This is an optional value, normally is automatically detected during setup phase and so is not required to specify it. You should try to configure this parameter only if the setup fail in the detection.<br/>
The device_name can be read using the TV Menu, normally select the menu General -> System Options<br/>
**Note: this parameter is used only during initial configuration and then stored in the registry. It's not possible to change the value after that the component is configured. To change the value you must before delete the integration from UI.**<br/>

- **mac:**<br/>
(string)(Optional)<br/>
This is an optional value, normally is automatically detected during setup phase and so is not required to specify it. You should try to configure this parameter only if the setup fail in the detection.<br/>
The mac-address is used to turn on the TV. If you set it manually, you must find the right value from the TV Menu or from your network router.<br/>

- **source_list:**<br/>
(json)(Optional)<br/>
This contains the KEYS visible sources in the dropdown list in media player UI.<br/>
Default value: '{"TV": "KEY_TV", "HDMI": "KEY_HDMI"}'<br/>
If SmartThings is [configured](https://github.com/ollo69/ha-samsungtv-smart/blob/master/Smartthings.md) and the source_list not, the component will try to identify and configure automatically the sources configured on the TV with the relative associated names (new feature, tested on QLed TV). The created list is available in the HA log file.<br/>
You can also chain KEYS, example: '{"TV": "KEY_SOURCES+KEY_ENTER"}'<br/>
And even add delays (in milliseconds) between sending KEYS, example:<br/>
    '{"TV": "KEY_SOURCES+500+KEY_ENTER"}'<br/>
Resources: [key codes](https://github.com/ollo69/ha-samsungtv-smart/blob/master/Key_codes.md) / [key patterns](https://github.com/ollo69/ha-samsungtv-smart/blob/master/Key_chaining.md)<br/>
**Warning: changing input source with voice commands only works if you set the device name in `source_list` as one of the whitelisted words that can be seen on [this page](https://web.archive.org/web/20181218120801/https://developers.google.com/actions/reference/smarthome/traits/modes#mode-settings) (under "Mode Settings")**<br/>
    
- **app_list:**<br/>
(json)(Optional)<br/>
This contains the APPS visible sources in the dropdown list in media player UI.<br/>
Default value: AUTOGENERATED<br/>
Autogenerated list is limited to few application (the most diffused). To have other applications, the list must be set manually.
If the source_list is not configured, during startup is generated a file in the custom component folder with the list of all available applications. This list can be used to create a manual list following [app_list guide](https://github.com/ollo69/ha-samsungtv-smart/blob/master/App_list.md)<br/>
Example value: '{"Netflix": "11101200001", "YouTube": "111299001912", "Spotify": "3201606009684"}'<br/>
Known lists of App IDs: [List 1](https://github.com/tavicu/homebridge-samsung-tizen/issues/26#issuecomment-447424879), [List 2](https://github.com/Ape/samsungctl/issues/75#issuecomment-404941201)<br/>
**Note: Although this setting is optional, it is highly recommended (for best performance) to set it manually and not rely on the autogenerated list.**<br/>

- **update_method:**<br/>
(string)(Optional)<br/>
This change the ping method used for state update. Values: "ping", "websockets" and "smartthings"<br/>
Default value: "ping" if SmartThings is not enabled, else "smartthings"<br/>
Example update_method: "websockets"<br/>
    
- **update_custom_ping_url:**<br/>
(string)(Optional)<br/>
Use custom endpoint to ping.<br/>
Default value: PING TO 8001 ENDPOINT<br/>
Example update_custom_ping_url: "http://192.168.1.77:9197/dmr"<br/>
    
- **show_channel_number:**<br/>
(boolean)(Optional)<br/>
If the SmartThings API is enabled (by settings "api_key" and "device_id"), then the TV Channel Names will show as media titles, by setting this to True the TV Channel Number will also be attached to the end of the media title. (when applicable)

- **scan_app_http:**<br/>
(boolean)(Optional)<br/>
This option is `True` by default. In some cases (if numerical IDs are used when setting `app_list`) HTTP polling will be used (1 request per app) to get the running app.<br/>
This is a lengthy task that some may want to disable, you can do so by setting this option to `False`.<br/>
For more information about how we get the running app, read the [app_list guide](https://github.com/ollo69/ha-samsungtv-smart/blob/master/App_list.md).<br/>

- **broadcast_address:**<br/>
(string)(Optional)<br/>
**Do not set this option if you do not know what it does, it can break turning your TV on.**<br/>
The ip address of the host to send the magic packet (for wakeonlan) to if the "mac" property is also set.<br/>
Default value: "255.255.255.255"<br/>
Example value: "192.168.1.255"<br/>

# Usage

### Known Supported Voice Commands

* Turn on `SAMSUNG-TV-NAME-HERE` (for some older TVs this only works if the TV is connected by LAN cable to the Network)
* Turn off `SAMSUNG-TV-NAME-HERE`
* Volume up on `SAMSUNG-TV-NAME-HERE` (increases volume by 1)
* Volume down on `SAMSUNG-TV-NAME-HERE` (decreases volume by 1)
* Set volume to 50 on `SAMSUNG-TV-NAME-HERE` (sets volume to 50 out of 100)
* Mute `SAMSUNG-TV-NAME-HERE` (sets volume to 0)
* Change input source to `DEVICE-NAME-HERE` on `SAMSUNG-TV-NAME-HERE` (only works if `DEVICE-NAME-HERE` is a whitelisted word from [this page](https://web.archive.org/web/20181218120801/https://developers.google.com/actions/reference/smarthome/traits/modes) under "Mode Settings")

(if you find more supported voice commands, please create an issue so I can add them here)

### Cast to TV

`service: media_player.play_media`

```
{
  "entity_id": "media_player.samsungtv",
  "media_content_type": "url",
  "media_content_id": "FILE_URL",
}
```
_Replace FILE_URL with the url of your file._

### Send Keys
```
service: media_player.play_media
```

```json
{
  "entity_id": "media_player.samsungtv",
  "media_content_type": "send_key",
  "media_content_id": "KEY_CODE",
}
```
**Note**: Change "KEY_CODEKEY" by desired key_code. (also works with key chaining and SmartThings keys: ST_TV, ST_HDMI1, ST_HDMI2, ST_HDMI3, etc. / see more at [SmartThings Keys](https://github.com/ollo69/ha-samsungtv-smart/blob/master/Smartthings.md#smartthings-keys))

Script example:
```
tv_channel_down:
  alias: Channel down
  sequence:
  - service: media_player.play_media
    data:
      entity_id: media_player.samsung_tv55
      media_content_id: KEY_CHDOWN
      media_content_type: "send_key"
```


***Key Codes***
---------------
To see the complete list of known keys, [check this list](https://github.com/ollo69/ha-samsungtv-smart/blob/master/Key_codes.md)


***Key Chaining Patterns***
---------------
Key chaining is also supported, which means a pattern of keys can be set by delimiting the keys with the "+" symbol, delays can also be set in milliseconds between the "+" symbols.

[See the list of known Key Chaining Patterns](https://github.com/ollo69/ha-samsungtv-smart/blob/master/Key_chaining.md)


***Open Browser Page***
---------------

```
service: media_player.play_media
```

```json
{
  "entity_id": "media_player.samsungtv",
  "media_content_type": "browser",
  "media_content_id": "https://www.google.com",
}
```
