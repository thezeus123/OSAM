# OSAM - Frequently Asked Questions (FAQ)

## 📦 Setup & Installation

### Q: Do I need to create helper entities manually?

**A:** Yes. OSAM requires helper entities that you create in Home Assistant:

1. **Input Boolean** - For repeat logic (required)
2. **Input Text** - For JSON storage (required)
3. **Input Boolean** - For snooze (optional)

Create them via: **Settings** → **Devices & Services** → **Helpers**

### Q: How many JSON storage entities do I need?

**A:** **One per sensor is recommended.**

**Why?**
- Simple to manage
- Default 255 character limit (via GUI) is sufficient
- No YAML editing required
- Easier troubleshooting

**Examples:**
- Front Door → `input_text.osam_front_door_json`
- Bedroom Window → `input_text.osam_bedroom_window_json`
- Garage Door → `input_text.osam_garage_door_json`

### Q: Can I use one shared JSON storage for all sensors?

**A:** Yes, but **not recommended** unless you have 10+ sensors.

**Requirements for shared storage:**
- Must manually edit `configuration.yaml`
- Must set max length to 10000+
- More complex troubleshooting

```yaml
# configuration.yaml
input_text:
  osam_json_storage_shared:
    name: "OSAM Shared Storage"
    max: 10000
```

### Q: Why does the documentation say "10000+ characters"?

**A:** That was the old recommendation when using **shared storage** for multiple sensors. 

**Current best practice:** One storage per sensor = 255 chars is enough!

### Q: Do I need to increase the max length to 10000?

**A:** **No**, if using one storage per sensor (recommended).

**Yes**, only if using shared storage for multiple sensors.

## 🔔 Notifications

### Q: I'm not receiving any notifications. Why?

**A:** Check these common issues:

1. **Quiet Time active?** Check your quiet time settings
2. **Daily Limit reached?** Check if you hit the daily notification limit
3. **Temperature check enabled?** Verify outdoor temperature is below threshold
4. **Snooze active?** Check if snooze helper is turned on
5. **Notification service exists?** Verify `notify.mobile_app_phone` or your custom script exists

**Enable Debug Mode** and check logs: **Settings** → **System** → **Logs**, filter by `osam.[sensor_name]`

### Q: Can I use OSAM without custom notification scripts?

**A:** **Yes!** Leave `notification_script` empty and OSAM will automatically use Home Assistant's built-in `persistent_notification`. No setup needed.

### Q: How do I use Alexa for notifications?

**A:** Two options:

**Option 1: Simple (No custom script)**
```yaml
notify_services: "alexa_living_room"
notification_script: ""  # Leave empty
```
Uses Alexa Media Player's notify service directly.

**Option 2: Advanced (With custom script)**
Create a script that handles Alexa TTS with volume control. See [NOTIFICATION_SCRIPTS.md](NOTIFICATION_SCRIPTS.md) for examples.

## ⚙️ Configuration

### Q: What's the difference between "Detection Grace Period" and "First Notification Delay"?

**A:** They serve different purposes:

- **Detection Grace Period**: Time sensor must be in issue state before OSAM even starts
  - Example: Door must be open for 5 minutes before detection
  - Prevents false alarms from brief openings

- **First Notification Delay**: Additional delay AFTER detection before first notification
  - Example: After detection, wait another 2 minutes before notifying
  - Useful for giving time to close door after detection

**Total time before first notification = Grace Period + First Notification Delay**

### Q: What does "Issue State" mean?

**A:** The sensor state that triggers the alert.

- **On (Open)**: Alert when sensor is "on" (typical for doors/windows)
- **Off (Closed)**: Alert when sensor is "off" (useful for detecting break-ins or unexpected closures)

### Q: How do I change the language from German to English?

**A:** See the [LOCALIZATION.md](LOCALIZATION.md) guide. Quick method:

Edit blueprint line ~567:
```yaml
# Change this:
opened_duration: "{{ opened_duration_DE }}"

# To this:
opened_duration: "{{ opened_duration_EN }}"
```

## 🚨 Escalation

### Q: When does escalation trigger?

**A:** After **X notifications** (not time-based).

Example: If `escalation_after_count: 6`, escalation triggers after the 6th notification is sent.

### Q: Do escalations ignore the daily limit?

**A:** Yes! Escalations **always send**, even if daily limit is reached. However, they still increment the counter.

### Q: Can escalations override quiet time?

**A:** Yes, if you enable `escalation_override_quiet_time: true` (default).

Critical alerts will be sent even during quiet hours.

### Q: Will escalation repeat or just send once?

**A:** Depends on `escalation_repeat_enabled`:

- **false** (default): Escalation sends once
- **true**: Escalation repeats on every repeat interval

## 🌡️ Temperature Check

### Q: Does temperature check work without a temperature sensor?

**A:** If `enable_temperature_check: true` but no sensor is configured, OSAM will **allow all notifications** (fail-safe mode).

### Q: What happens if my temperature sensor is unavailable?

**A:** OSAM will treat it as "OK" and send notifications normally (fail-safe).

## 🔄 Repeats & Snooze

### Q: How does snooze work?

**A:** Snooze is **counter-based**, not time-based.

Example: `snooze_count: 5`
- Turn on snooze helper
- Next 5 notifications are suppressed
- After 5 notifications: snooze automatically turns off

### Q: Can I snooze indefinitely?

**A:** No. Once the snooze counter reaches zero, snooze automatically disables. You'd need to manually enable it again.

### Q: Why counter-based snooze instead of time-based?

**A:** More predictable! "Snooze for 3 notifications" is clearer than "snooze for 30 minutes" when repeat intervals might vary.

## 🐛 Troubleshooting

### Q: Blueprint fails to load with YAML error

**A:** Common causes:

1. **Emoji characters in older versions** - Fixed in v3.12.1, update to latest
2. **Incorrect indentation** - YAML is sensitive to spaces
3. **Copy-paste issues** - Download file directly instead of copy-paste from browser

### Q: Automation triggers but nothing happens

**A:** Enable **Debug Mode** in the automation configuration. Then check:

**Settings** → **System** → **Logs**

Filter by `osam.[your_sensor_name]` to see detailed logs of what's happening.

### Q: Helper entity not found error

**A:** Ensure you created the helper entities:

1. Go to **Settings** → **Devices & Services** → **Helpers**
2. Verify the input_boolean and input_text exist
3. Check the entity IDs match your configuration exactly

### Q: Escalation not working

**A:** Check:

1. Is `enable_escalation: true`?
2. Has the notification counter reached `escalation_after_count`?
3. If using quiet time, is `escalation_override_quiet_time: true`?
4. Check Debug Mode logs for escalation trigger messages

## 🔧 Advanced

### Q: Can I use OSAM with input_boolean instead of binary_sensor?

**A:** Yes! OSAM works with any entity that has an on/off state:
- `binary_sensor.*`
- `input_boolean.*`
- `switch.*` (if you want to monitor switch state)

### Q: How do I monitor a sensor for being CLOSED instead of OPEN?

**A:** Set `issue_state: "off"`

This will trigger when sensor is in "off" state (closed). Useful for detecting:
- Doors that should always be open
- Break-in detection (sensor goes off unexpectedly)
- Power loss (switch goes off)

### Q: Can I have different notifications for different times of day?

**A:** Not directly in OSAM, but you can:

1. **Create custom action** that checks time and sends different message
2. **Use templates** in notification messages with time-based conditions
3. **Create multiple automations** with different quiet time settings

### Q: What variables can I use in notification templates?

**A:** You can use these variables in notification titles and messages:

| Variable | Output Example |
|----------|----------------|
| `<friendly_name>` | "Front Door" |
| `<opened_duration>` | "2 hours and 15 minutes" |
| `<counter>` | "8" |
| `<counter_ordinal>` | "eighth" |

**Example usage:**
```yaml
notification_msg_first: |
  WARNING: <friendly_name> has been open!
  Duration: <opened_duration>
  This is notification #<counter> (the <counter_ordinal>)
```

**Advanced:** Use language-specific Jinja variables directly:
```yaml
notification_msg_first: |
  WARNING: <friendly_name> has been open for {{ opened_duration_EN }}!
  This is the {{ counter_ordinal_EN }} notification.
```

See the [Configuration Guide](README.md#notification-settings) for more details.

### Q: How many sensors can OSAM handle?

**A:** **Unlimited!** Each automation monitors one sensor. Create as many automations as you need.

If using shared JSON storage, you might hit storage limits with 50+ sensors. Use one storage per sensor to avoid this.

## 🆘 Still Need Help?

- 🐛 **Bug Reports**: [GitHub Issues](https://github.com/thezeus123/OSAM/issues)
- 💡 **Feature Requests**: [GitHub Discussions](https://github.com/thezeus123/OSAM/discussions)
- 💬 **Community Support**: [Home Assistant Forum](https://community.home-assistant.io/c/blueprints-exchange/53)
- 📖 **Documentation**: [GitHub README](https://github.com/thezeus123/OSAM)

---

**Didn't find your question?** Ask in the [Home Assistant Community Forum](https://community.home-assistant.io/) or open an issue on GitHub!
