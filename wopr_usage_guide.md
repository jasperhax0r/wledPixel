# WOPR Mode Usage Guide

## Overview
The WOPR mode adds a WarGames-inspired random blinking effect to your LED matrix display. It creates dynamic, computing-style patterns similar to the iconic WOPR computer from the 1983 film.

## Features
- **Random LED patterns** - Multiple blinking patterns including sweeps, checkerboards, and random flickers
- **Dynamic intensity** - Automatic brightness variations for that authentic computing feel
- **Configurable speed** - Adjust update interval from 50ms to 1000ms
- **Per-zone control** - Each zone can run independently

## Remote Control via MQTT

### Switch to WOPR Mode
```bash
# For Zone 0
mosquitto_pub -h YOUR_MQTT_SERVER -t "wledPixel-XXXX/zone0/workmode" -m "wopr"

# For Zone 1
mosquitto_pub -h YOUR_MQTT_SERVER -t "wledPixel-XXXX/zone1/workmode" -m "wopr"

# For Zone 2
mosquitto_pub -h YOUR_MQTT_SERVER -t "wledPixel-XXXX/zone2/workmode" -m "wopr"
```

### Send Custom Text (switches to manual mode)
```bash
mosquitto_pub -h YOUR_MQTT_SERVER -t "wledPixel-XXXX/zone0/text" -m "GREETINGS PROFESSOR FALKEN"
```

### Switch Between Modes
```bash
# Clock mode
mosquitto_pub -h YOUR_MQTT_SERVER -t "wledPixel-XXXX/zone0/workmode" -m "wallClock"

# Manual text mode
mosquitto_pub -h YOUR_MQTT_SERVER -t "wledPixel-XXXX/zone0/workmode" -m "manualInput"

# WOPR mode
mosquitto_pub -h YOUR_MQTT_SERVER -t "wledPixel-XXXX/zone0/workmode" -m "wopr"

# Weather mode
mosquitto_pub -h YOUR_MQTT_SERVER -t "wledPixel-XXXX/zone0/workmode" -m "owmWeather"
```

## Web Interface Control

### Via HTTP API
```bash
# Switch to WOPR mode
curl -X POST "http://YOUR_ESP_IP/api/config" \
  -d "key=zoneSettings" \
  -d "zone=0" \
  -d "workMode=wopr"

# Adjust WOPR speed (lower = faster)
curl -X POST "http://YOUR_ESP_IP/api/config" \
  -d "key=zoneSettings" \
  -d "zone=0" \
  -d "woprUpdateInterval=150"

# Send custom text
curl -X POST "http://YOUR_ESP_IP/api/message" \
  -d "messageZone0=SHALL WE PLAY A GAME?"
```

## Pattern Types

The WOPR mode includes 5 different random patterns:

1. **Full Random** - Completely random LED states
2. **Horizontal Sweep** - Wave-like patterns moving horizontally
3. **Vertical Sweep** - Wave-like patterns moving vertically  
4. **Checkerboard Flicker** - Animated checkerboard patterns
5. **Intensity Blocks** - Random vertical blocks of light

The display randomly switches between these patterns creating an authentic computing effect.

## Configuration Parameters

- **updateInterval**: Controls how fast the patterns change (50-1000ms recommended)
  - 50ms = Very fast, chaotic
  - 100ms = Standard WOPR speed (default)
  - 200ms = Slower, more deliberate
  - 500ms+ = Slow, methodical

## Tips

1. **Multiple Zones**: Run WOPR mode on one zone while displaying text on another for a cool effect
2. **Intensity**: The mode automatically varies intensity - disable this by commenting out the intensity change code if you prefer constant brightness
3. **Random Seed**: The ESP32 generates pseudo-random patterns - reset it for different sequences

## Troubleshooting

**Display is blank in WOPR mode:**
- Check that the zone is properly configured
- Verify `woprZones[n].active` is set to true
- Ensure `P.displayClear(n)` isn't being called repeatedly

**Patterns are too fast/slow:**
- Adjust `woprUpdateInterval` via MQTT or web API
- Default is 100ms, try values between 50-500ms

**MQTT not working:**
- Verify MQTT is enabled in settings
- Check MQTT server address and credentials
- Monitor serial output for connection status

## Home Assistant Integration

The WOPR mode is automatically added to Home Assistant discovery:

```yaml
# Example automation
automation:
  - alias: "WOPR Mode at Night"
    trigger:
      platform: time
      at: "22:00:00"
    action:
      service: select.select_option
      target:
        entity_id: select.wledpixel_xxxx_zone0_work_mode
      data:
        option: wopr
```
