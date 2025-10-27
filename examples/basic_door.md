# Example: Basic Door Monitoring

This is a simple configuration for monitoring a front door that should be closed.

## Configuration

```yaml
# Sensor to monitor
trigger_entity: binary_sensor.front_door

# Display name
friendly_name: "Front Door"

# Alert when open (On state)
issue_state: "on"

# Wait 5 minutes before first detection
duration_issue_state: 5

# No additional delay before first notification
first_notification_delay: 0

# Consider closed after 2 minutes
duration_resolved_state: 120

# Required helpers
helper_entity: input_boolean.osam_front_door_helper
json_storage: input_text.osam_front_door_json  # One per sensor (255 chars sufficient)

# Notification settings
notify_services: "mobile_app_phone"
notification_script: "script.zentrale_benachrichtigung"
notification_delete_script: "script.benachrichtigung_loschen"

# Notification messages
notification_title: "WARNING: <friendly_name> is open!"
notification_msg_first: |
  <friendly_name> has been open for <opened_duration>!
notification_msg_repeat: |
  <friendly_name> is still open for <opened_duration>!
  This is reminder #<counter>.
notification_msg_after_silence: |
  Good morning! <friendly_name> is still open for <opened_duration>!

# NOTE: Available variables in templates:
# <friendly_name>     - Display name (e.g., "Front Door")
# <opened_duration>   - Human-readable time (e.g., "2 hours and 15 minutes")
# <counter>           - Notification number (e.g., "8")
# <counter_ordinal>   - Ordinal number (e.g., "eighth")

# Alexa volume
alexa_volume: 50

# Auto-delete notification when closed
delete_notification: true

# Repeat settings
repeat_enabled: true
repeat_duration: 15

# Quiet Time (optional)
enable_quiet_time: true
silence_starttime_workday: "22:00:00"
silence_endtime_workday: "07:00:00"
silence_starttime_nonworkday: "23:00:00"
silence_endtime_nonworkday: "09:00:00"
workday_entity: ""

# Snooze disabled
enable_snooze: false
snooze_helper: ""
snooze_count: 3

# Temperature check disabled
enable_temperature_check: false
outdoor_temperature_sensor: ""
outdoor_temperature_threshold: 10

# No daily limit
enable_notification_limit: false
notification_daily_limit: 0

# No escalation
enable_escalation: false
escalation_after_count: 6
escalation_override_quiet_time: true
escalation_repeat_enabled: false
escalation_notify_services: ""
escalation_alexa_volume: 80
escalation_title: "CRITICAL: <friendly_name> open!"
escalation_message: |
  <friendly_name> has been open for <opened_duration>!
  This is a critical warning - please close immediately!

# No custom actions
custom_action_escalation: []
custom_action_on_send_notification: []
custom_action_repeat_notification: []
custom_action_from_issue_state: []

# Debug disabled
debug_mode: false
```

## Expected Behavior

1. **Door opens** → 5 minute grace period starts
2. **After 5 minutes** → First notification sent
3. **Every 15 minutes** → Repeat notification (while still open)
4. **During quiet time** (22:00-07:00) → No notifications
5. **When quiet time ends** (07:00) → Reminder notification
6. **Door closes** → Wait 2 minutes, then stop and delete notification

## Use Case

Perfect for:
- Main entrance doors
- Security monitoring
- Standard door monitoring without special requirements
