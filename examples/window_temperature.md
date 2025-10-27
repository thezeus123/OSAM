# Example: Window with Temperature Check

Monitor a bedroom window and only send notifications when it's cold outside (energy saving).

## Configuration

```yaml
# Sensor to monitor
trigger_entity: binary_sensor.bedroom_window

# Display name
friendly_name: "Bedroom Window"

# Alert when open
issue_state: "on"

# Wait 10 minutes before detection
duration_issue_state: 10

# No additional delay
first_notification_delay: 0

# Consider closed after 2 minutes
duration_resolved_state: 120

# Required helpers
helper_entity: input_boolean.osam_bedroom_window_helper
json_storage: input_text.osam_bedroom_window_json  # One per sensor (255 chars sufficient)

# Notifications
notify_services: "mobile_app_phone,alexa_bedroom"
notification_script: "script.zentrale_benachrichtigung"
notification_delete_script: "script.benachrichtigung_loschen"

# Messages
notification_title: "WARNING: <friendly_name> is open and it's cold!"
notification_msg_first: |
  <friendly_name> has been open for <opened_duration>!
  Current outdoor temperature is below 10°C.
  Please close to save energy!
notification_msg_repeat: |
  Reminder: <friendly_name> is still open!
  Duration: <opened_duration>
  It's cold outside - you're wasting heating energy.
notification_msg_after_silence: |
  Good morning! <friendly_name> was left open overnight.
  Duration: <opened_duration>

# NOTE: Available variables:
# <friendly_name>, <opened_duration>, <counter>, <counter_ordinal>
# You can also use language-specific variables like {{ opened_duration_EN }}

# Alexa volume
alexa_volume: 40

# Auto-delete when closed
delete_notification: true

# Repeat settings
repeat_enabled: true
repeat_duration: 20

# Quiet Time enabled
enable_quiet_time: true
silence_starttime_workday: "22:00:00"
silence_endtime_workday: "07:00:00"
silence_starttime_nonworkday: "23:00:00"
silence_endtime_nonworkday: "09:00:00"
workday_entity: "binary_sensor.workday"

# Snooze enabled
enable_snooze: true
snooze_helper: "input_boolean.osam_bedroom_window_snooze"
snooze_count: 5

# Temperature check ENABLED
enable_temperature_check: true
outdoor_temperature_sensor: "sensor.outdoor_temperature"
outdoor_temperature_threshold: 10

# Daily limit to prevent spam
enable_notification_limit: true
notification_daily_limit: 10

# No escalation (not critical)
enable_escalation: false
escalation_after_count: 6
escalation_override_quiet_time: false
escalation_repeat_enabled: false
escalation_notify_services: ""
escalation_alexa_volume: 80
escalation_title: "CRITICAL: <friendly_name> open!"
escalation_message: ""

# Custom action: Turn on bedroom light on first notification
custom_action_on_send_notification:
  - service: light.turn_on
    target:
      entity_id: light.bedroom
    data:
      brightness: 128

# Turn off light when window closes
custom_action_from_issue_state:
  - service: light.turn_off
    target:
      entity_id: light.bedroom

# No other custom actions
custom_action_escalation: []
custom_action_repeat_notification: []

# Debug disabled
debug_mode: false
```

## Expected Behavior

### Scenario 1: Window open, warm outside (>10°C)
- **No notifications** - Temperature check prevents them

### Scenario 2: Window open, cold outside (<10°C)
1. **Window opens** → 10 minute grace period
2. **After 10 minutes** → Check temperature
3. **If <10°C** → Send notification + turn on bedroom light
4. **Every 20 minutes** → Repeat notification (if still <10°C)
5. **During quiet time** → No notifications
6. **Window closes** → Turn off bedroom light, delete notification

### Scenario 3: Snooze active
- **Suppress next 5 notifications**
- **Auto-disable snooze** after 5 notifications suppressed

## Use Case

Perfect for:
- Energy-saving alerts
- Bedroom/living room windows
- Climate-controlled rooms
- Situations where open windows only matter when it's cold

## Customization Ideas

- Adjust temperature threshold based on season
- Add custom action to send graph of energy consumption
- Integrate with heating system to reduce temperature when window open
- Use different thresholds for day/night
