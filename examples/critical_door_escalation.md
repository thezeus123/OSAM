# Example: Critical Door with Escalation

Security-focused configuration for a door that must be closed, with escalating alerts.

## Configuration

```yaml
# Sensor to monitor
trigger_entity: binary_sensor.front_door

# Display name
friendly_name: "Front Door"

# Alert when open
issue_state: "on"

# Short grace period (2 minutes)
duration_issue_state: 2

# No delay - immediate notification after detection
first_notification_delay: 0

# Quick close detection (30 seconds)
duration_resolved_state: 30

# Required helpers
helper_entity: input_boolean.osam_front_door_helper
json_storage: input_text.osam_front_door_json  # One per sensor (255 chars sufficient)

# Multiple notification targets
notify_services: "mobile_app_phone,mobile_app_tablet,alexa_living_room"
notification_script: "script.zentrale_benachrichtigung"
notification_delete_script: "script.benachrichtigung_loschen"

# Normal notification messages
notification_title: "WARNING: <friendly_name> is open!"
notification_msg_first: |
  <friendly_name> has been open for <opened_duration>!
  Please check if this is intentional.
notification_msg_repeat: |
  REMINDER #<counter>: <friendly_name> is STILL open!
  Duration: <opened_duration>
  Please close immediately if left open accidentally.
notification_msg_after_silence: |
  ALERT: <friendly_name> was left open overnight!
  Total duration: <opened_duration>

# TIP: You can use all these variables in your messages:
# <friendly_name>     - "Front Door"
# <opened_duration>   - "2 hours and 15 minutes"
# <counter>           - "8"
# <counter_ordinal>   - "eighth"

# Normal Alexa volume
alexa_volume: 60

# Auto-delete when closed
delete_notification: true

# Frequent repeats
repeat_enabled: true
repeat_duration: 10

# Quiet Time with override for escalations
enable_quiet_time: true
silence_starttime_workday: "22:00:00"
silence_endtime_workday: "07:00:00"
silence_starttime_nonworkday: "23:00:00"
silence_endtime_nonworkday: "09:00:00"
workday_entity: "binary_sensor.workday"

# Snooze disabled (security door)
enable_snooze: false
snooze_helper: ""
snooze_count: 3

# No temperature check
enable_temperature_check: false
outdoor_temperature_sensor: ""
outdoor_temperature_threshold: 10

# No daily limit (security)
enable_notification_limit: false
notification_daily_limit: 0

# ESCALATION ENABLED
enable_escalation: true
escalation_after_count: 6
escalation_override_quiet_time: true
escalation_repeat_enabled: true
escalation_notify_services: "mobile_app_phone,mobile_app_tablet,alexa_living_room,alexa_bedroom"
escalation_alexa_volume: 100
escalation_title: "🚨 CRITICAL ALERT: <friendly_name> OPEN!"
escalation_message: |
  ⚠️ CRITICAL SECURITY ALERT ⚠️
  
  <friendly_name> has been open for <opened_duration>!
  
  This is notification #<counter> (<counter_ordinal>).
  
  IMMEDIATE ACTION REQUIRED:
  - Check security cameras
  - Verify if door should be open
  - Close door if left open accidentally
  
  This is an automated security alert.

# Custom Actions

# First notification: Turn on entrance light
custom_action_on_send_notification:
  - service: light.turn_on
    target:
      entity_id: light.entrance
    data:
      brightness: 255

# Repeat: Flash entrance light
custom_action_repeat_notification:
  - service: light.turn_on
    target:
      entity_id: light.entrance
    data:
      flash: short

# Escalation: Multiple actions
custom_action_escalation:
  # Flash all house lights
  - service: light.turn_on
    target:
      entity_id: 
        - light.living_room
        - light.kitchen
        - light.bedroom
        - light.entrance
    data:
      flash: long
  
  # Activate siren (if available)
  - service: switch.turn_on
    target:
      entity_id: switch.alarm_siren
  
  # Send critical notification to additional device (e.g., partner's phone)
  - service: notify.mobile_app_partner_phone
    data:
      title: "🚨 CRITICAL: Front Door Open!"
      message: "Front door has been open for a long time. Check security!"
      data:
        priority: high
        ttl: 0
        channel: alarm_stream
        importance: high
  
  # Log to persistent notification for history
  - service: persistent_notification.create
    data:
      title: "Security Log"
      message: "Escalation triggered for front door at {{ now().strftime('%H:%M:%S') }}"
      notification_id: "osam_security_log"

# Door closed: Turn off lights and siren
custom_action_from_issue_state:
  - service: light.turn_off
    target:
      entity_id: 
        - light.entrance
        - light.living_room
  - service: switch.turn_off
    target:
      entity_id: switch.alarm_siren

# Debug enabled for security monitoring
debug_mode: true
```

## Expected Behavior

### Timeline

**Minute 0**: Door opens
- Grace period starts (2 minutes)

**Minute 2**: Detection complete
- First notification sent
- Entrance light turns on

**Minute 12**: First repeat (after 10 minutes)
- Notification #2 sent
- Entrance light flashes

**Minute 22, 32, 42, 52**: Subsequent repeats
- Notifications #3, #4, #5, #6 sent
- Light continues flashing

**Minute 62**: ESCALATION TRIGGERED (6th notification)
- 🚨 Critical alert sent to all devices
- All house lights flash
- Alarm siren activates
- Partner notified via phone
- Security log created
- **Escalation repeats every 10 minutes** (escalation_repeat_enabled: true)

**During Quiet Time**: 
- Normal notifications suppressed
- Escalations still sent (override enabled)

**Door Closes**:
- All lights turn off
- Siren deactivates
- Notifications deleted
- Wait 30 seconds before considering fully closed

## Use Case

Perfect for:
- Main entrance security monitoring
- Doors that should always be closed
- High-security areas
- Situations requiring immediate response

## Security Features

✅ **Fast Detection** - 2 minute grace period  
✅ **Frequent Reminders** - Every 10 minutes  
✅ **Escalation** - After 1 hour (6 notifications)  
✅ **Night Alerts** - Escalations override quiet time  
✅ **Visual Warnings** - Lights flash  
✅ **Audio Warnings** - Siren for escalations  
✅ **Multi-Device** - Phone, tablet, Alexa  
✅ **Logging** - Debug mode + persistent notifications  

## Customization Ideas

- Add camera snapshot to escalation notification
- Integrate with security system
- Send SMS for escalations
- Create security event log
- Add geo-fencing (only alert when away from home)
