# Custom Notification Scripts - Integration Guide

OSAM supports custom notification scripts, allowing you to use any notification system you prefer. This guide explains how to create or adapt your own notification scripts.

## 📋 Overview

OSAM can use two types of notification scripts:

1. **Notification Script** - Sends notifications
2. **Notification Delete Script** - Deletes/dismisses notifications

Both are **optional**. If not provided, OSAM falls back to `persistent_notification`.

## 🔔 Notification Script

### Required Parameters

Your notification script must accept these parameters:

| Parameter | Type | Description | Example |
|-----------|------|-------------|---------|
| `devices` | string | Comma-separated list of target devices | `"mobile_app_phone,alexa_echo"` |
| `titel` | string | Notification title | `"WARNING: Front Door is open!"` |
| `nachricht` | string | Notification message | `"Front Door has been open for 2 hours!"` |
| `vorlage` | string | Template/Priority | `"default"` or `"alarm"` |
| `priority` | string | Priority level | `"normal"` or `"high"` |
| `alexa_lautstaerke` | float | Alexa volume (0.0-1.0) | `0.5` (= 50%) |
| `tag` | string | Notification ID for updates | `"osam_binary_sensor_front_door"` |

### Example Script Structure

```yaml
zentrale_benachrichtigung:
  alias: "Central Notification Handler"
  description: "Unified notification script for OSAM"
  fields:
    devices:
      description: "Target devices (comma-separated)"
      example: "mobile_app_phone,alexa_echo"
    titel:
      description: "Notification title"
      example: "Warning"
    nachricht:
      description: "Notification message"
      example: "Door is open"
    vorlage:
      description: "Template type"
      example: "default"
    priority:
      description: "Priority level"
      example: "normal"
    alexa_lautstaerke:
      description: "Alexa volume (0.0-1.0)"
      example: 0.5
    tag:
      description: "Notification tag/ID"
      example: "osam_sensor_1"
  
  sequence:
    # Parse devices list
    - variables:
        device_list: "{{ devices.split(',') }}"
    
    # Send to each device
    - repeat:
        for_each: "{{ device_list }}"
        sequence:
          - choose:
              # Mobile App Notifications
              - conditions:
                  - condition: template
                    value_template: "{{ repeat.item.startswith('mobile_app_') }}"
                sequence:
                  - service: "notify.{{ repeat.item }}"
                    data:
                      title: "{{ titel }}"
                      message: "{{ nachricht }}"
                      data:
                        tag: "{{ tag }}"
                        priority: "{{ 'high' if priority == 'high' else 'normal' }}"
                        ttl: 0
                        importance: "{{ 'high' if vorlage == 'alarm' else 'default' }}"
                        notification_icon: "{{ 'mdi:alert' if vorlage == 'alarm' else 'mdi:information' }}"
              
              # Alexa TTS
              - conditions:
                  - condition: template
                    value_template: "{{ repeat.item.startswith('alexa_') }}"
                sequence:
                  - service: notify.alexa_media
                    data:
                      target: "{{ repeat.item }}"
                      message: "{{ nachricht }}"
                      data:
                        type: tts
                        method: speak
                        volume: "{{ alexa_lautstaerke | float }}"
              
              # Telegram
              - conditions:
                  - condition: template
                    value_template: "{{ repeat.item == 'telegram' }}"
                sequence:
                  - service: notify.telegram
                    data:
                      title: "{{ titel }}"
                      message: "{{ nachricht }}"
```

### Simple Alternative (Persistent Notification Fallback)

If you don't provide a custom script, OSAM uses:

```yaml
- service: persistent_notification.create
  data:
    title: "{{ msg_title }}"
    message: "{{ msg_body }}"
    notification_id: "{{ notification_id }}"
```

## 🗑️ Notification Delete Script

### Required Parameters

Your delete script must accept these parameters:

| Parameter | Type | Description | Example |
|-----------|------|-------------|---------|
| `devices` | string | Same devices as notification | `"mobile_app_phone,alexa_echo"` |
| `tag` | string | Notification ID to delete | `"osam_binary_sensor_front_door"` |

### Example Script Structure

```yaml
benachrichtigung_loschen:
  alias: "Delete Notification"
  description: "Removes notifications by tag"
  fields:
    devices:
      description: "Target devices"
      example: "mobile_app_phone"
    tag:
      description: "Notification tag to delete"
      example: "osam_sensor_1"
  
  sequence:
    - variables:
        device_list: "{{ devices.split(',') }}"
    
    - repeat:
        for_each: "{{ device_list }}"
        sequence:
          - choose:
              # Mobile App
              - conditions:
                  - condition: template
                    value_template: "{{ repeat.item.startswith('mobile_app_') }}"
                sequence:
                  - service: "notify.{{ repeat.item }}"
                    data:
                      message: "clear_notification"
                      data:
                        tag: "{{ tag }}"
              
              # For Alexa, do nothing (TTS can't be "deleted")
```

### Simple Alternative (Persistent Notification Fallback)

If you don't provide a custom delete script, OSAM uses:

```yaml
- service: persistent_notification.dismiss
  data:
    notification_id: "{{ notification_id }}"
```

## 🎯 Integration Examples

### Example 1: Using Only Mobile App

**OSAM Configuration:**
```yaml
notify_services: "mobile_app_phone"
notification_script: ""  # Leave empty to use persistent_notification
notification_delete_script: ""  # Leave empty
```

**What happens:**
- OSAM uses `persistent_notification.create`
- Notifications appear in Home Assistant UI
- Can be dismissed manually

### Example 2: Using Custom Script

**OSAM Configuration:**
```yaml
notify_services: "mobile_app_phone,alexa_living_room"
notification_script: "script.zentrale_benachrichtigung"
notification_delete_script: "script.benachrichtigung_loschen"
```

**What happens:**
- OSAM calls your custom script
- Your script handles device routing
- Your script can add custom logic (e.g., only TTS during day)

### Example 3: Mixed Approach

**OSAM Configuration:**
```yaml
notify_services: "mobile_app_phone"
notification_script: "script.my_notification"
notification_delete_script: ""  # Use built-in dismiss
```

**What happens:**
- Custom notification script for sending
- Built-in dismiss for deletion

## 🔧 Advanced Features You Can Add

### Priority-based Volume

```yaml
alexa_lautstaerke: >
  {% if priority == 'high' %}
    {{ 0.8 }}
  {% elif vorlage == 'alarm' %}
    {{ 1.0 }}
  {% else %}
    {{ alexa_lautstaerke | float }}
  {% endif %}
```

### Time-based TTS

```yaml
# Only speak via Alexa during daytime
- condition: template
  value_template: "{{ now().hour >= 8 and now().hour < 22 }}"
```

### Rich Notifications (Mobile)

```yaml
data:
  title: "{{ titel }}"
  message: "{{ nachricht }}"
  data:
    tag: "{{ tag }}"
    # Add action buttons
    actions:
      - action: "osam_snooze"
        title: "Snooze 1h"
      - action: "osam_disable"
        title: "Disable Today"
    # Add sound
    sound: "{{ 'alarm.mp3' if vorlage == 'alarm' else 'default' }}"
    # Add image
    image: "/local/icons/door_open.png"
```

### Multi-Language Support

```yaml
# Detect user language and translate
- variables:
    language: "{{ states('sensor.user_language') | default('en') }}"
    translated_title: >
      {% if language == 'de' %}
        WARNUNG: {{ titel }}
      {% elif language == 'fr' %}
        ATTENTION: {{ titel }}
      {% else %}
        {{ titel }}
      {% endif %}
```

## 📝 Parameter Reference

### vorlage (Template) Values

OSAM uses these template values:

| Value | Used For | Suggested Behavior |
|-------|----------|-------------------|
| `default` | Normal notifications | Standard notification style |
| `alarm` | Escalation notifications | Critical/urgent style, loud |

### priority (Priority) Values

| Value | Used For | Suggested Behavior |
|-------|----------|-------------------|
| `normal` | Regular notifications | Standard priority |
| `high` | Escalation notifications | High priority, bypass DND |

## 🚀 Getting Started Without Custom Scripts

You don't need custom scripts to use OSAM! Here's the quickest setup:

**Minimal Configuration:**
```yaml
notify_services: "mobile_app_phone"
notification_script: ""
notification_delete_script: ""
```

OSAM will automatically use Home Assistant's built-in `persistent_notification` system.

**Upgrade Path:**
1. Start with persistent notifications
2. Create custom script when you need more features
3. Update OSAM configuration to use your script
4. Existing automations automatically use new script

## 🆘 Troubleshooting

### Script not found error

**Error:** `Service script.zentrale_benachrichtigung not found`

**Solution:** 
- Either create the script
- Or leave `notification_script` empty to use built-in notifications

### Devices not receiving notifications

**Checklist:**
1. Verify device names are correct: `notify.mobile_app_phone`
2. Test device with simple notification: `Developer Tools → Services`
3. Check `devices` parameter is comma-separated (no spaces after comma)
4. Enable Debug Mode in OSAM to see what's being called

### Alexa volume not working

**Issue:** Alexa volume parameter ignored

**Solution:**
- Check your script converts `alexa_lautstaerke` (float 0-1) to correct format
- Some Alexa integrations need volume as integer 0-100
- Add conversion: `{{ (alexa_lautstaerke * 100) | int }}`

## 💡 Tips

1. **Start Simple**: Use built-in notifications first
2. **Test Separately**: Test your custom script independently before using with OSAM
3. **Log Everything**: Add logging to your script for debugging
4. **Fallback Logic**: Add fallback in your script if device is unavailable
5. **Reuse Scripts**: One notification script can serve multiple automations

## 📚 Related Documentation

- [Home Assistant Notification Services](https://www.home-assistant.io/integrations/#notifications)
- [Mobile App Notifications](https://companion.home-assistant.io/docs/notifications/notifications-basic)
- [Alexa Media Player](https://github.com/custom-components/alexa_media_player)
- [OSAM Examples](../examples/)

---

**Remember:** Custom scripts are optional! OSAM works perfectly fine with Home Assistant's built-in notification system.
