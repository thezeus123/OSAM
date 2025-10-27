# OSAM - Open Sensor Alert Manager - Advanced Contact Sensor Monitoring

## 🌟 Introduction

OSAM (Open Sensor Alert Manager) is a comprehensive blueprint for monitoring contact sensors (doors/windows) with advanced features like escalation management, quiet time, temperature checks, and much more.

I created this blueprint because I was tired of getting spammed by door/window notifications or missing critical alerts. OSAM gives you complete control over when, how, and how often you get notified.

## 📦 Installation

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fthezeus123%2FOSAM%2Fraw%2Fmain%2Fosam_v3.12.1.yaml)

**Or manually:**
```
https://github.com/thezeus123/OSAM/raw/main/osam_v3.12.1.yaml
```

## ✨ Key Features

### 🔔 Smart Notification System
* **Flexible repeat intervals** - Set how often to remind you
* **Multi-language support** - Duration in German, English, French, Italian
* **Custom notification scripts** - Use your own notification system
* **Message templates** - Fully customizable with variables

### 🚨 Escalation Management
* **Critical warnings** after X notifications
* **Separate devices** for escalations (e.g., SMS for critical alerts)
* **Override quiet time** for emergencies
* **Higher volume** for Alexa escalations
* **Ignores daily limits** but still tracks count

### 🌙 Quiet Time (Optional)
* **Separate schedules** for workdays and weekends
* **Workday sensor integration** for automatic detection
* **Completely optional** - Disable for 24/7 monitoring
* **Reminder notification** when quiet time ends

### ⏸️ Snooze Function
* **Counter-based** - Suppress X notifications instead of time-based
* **Automatic disable** when counter reaches zero
* **Per-automation** control with helper boolean

### 🌡️ Temperature Integration
* **Conditional notifications** - Only notify when cold outside
* **Configurable threshold** - Set your own temperature limit
* **Perfect for** "window open when it's freezing" alerts

### 📊 Daily Limits
* **Cap notifications** to prevent spam
* **Automatic reset** at midnight
* **Escalations ignore** limits (but count towards total)

### 🎯 Custom Actions
Execute automations at key points:
* First notification sent
* Repeat notification sent
* Escalation triggered
* Issue resolved

### 💾 JSON Storage
* **Unlimited sensors** with single input_text entity
* **Persistent data** across restarts
* **Tracks**: counter, first_opened, escalation_sent, snooze status

## 🎯 Real-World Examples

### Example 1: Front Door Security
```yaml
Detection Grace Period: 2 minutes
Repeat Interval: 10 minutes
Escalation: After 6 notifications
Escalation Override Quiet Time: Yes
```
**Why**: Critical security, escalate if ignored, alert even at night

### Example 2: Bedroom Window (Energy Saving)
```yaml
Detection Grace Period: 10 minutes
Enable Temperature Check: Yes
Temperature Threshold: 10°C
Quiet Time: 22:00 - 07:00
```
**Why**: Only care when it's cold, don't bug me at night

### Example 3: Garage Door
```yaml
Detection Grace Period: 30 minutes
Daily Limit: 5 notifications
Repeat Interval: 60 minutes
```
**Why**: Sometimes intentionally left open, don't spam

### Example 4: Freezer Door
```yaml
Detection Grace Period: 1 minute
Escalation: After 3 notifications
Escalation Volume: 100%
Custom Action (Escalation): Flash kitchen lights
```
**Why**: Critical! Food spoiling risk, immediate action needed

## 🔧 Setup Requirements

### Required Helpers (per automation)

**1. Input Boolean** (for repeat logic)
```yaml
Settings → Devices & Services → Helpers → Create Helper → Toggle
Name: osam_front_door_helper
```

**2. Input Text** (for JSON storage)
```yaml
Settings → Devices & Services → Helpers → Create Helper → Text
Name: osam_front_door_json
Maximum length: 255 (default is fine!)
```

**Important:** Create **one JSON storage per sensor** (recommended). The default 255 character limit via GUI is sufficient. Only use shared storage (10000+ chars) if you have many sensors and want to manage them in one entity.

### Optional Helpers

**3. Input Boolean** (for snooze)
```yaml
Name: osam_front_door_snooze
```

## 📖 Configuration Guide

### Available Variables in Templates

Use these variables in your notification titles and messages:

| Variable | Description | Example Output |
|----------|-------------|----------------|
| `<friendly_name>` | Sensor display name | "Front Door" |
| `<opened_duration>` | Time open (German) | "2 Stunden und 15 Minuten" |
| `<counter>` | Notification count | "8" |
| `<counter_ordinal>` | Ordinal (German) | "achte" |

### 🌍 Multi-Language: Use Language-Specific Variables!

**Want English notifications?** Just use `<opened_duration_en>` instead of `<opened_duration>`!

| Duration Variable | Language | Example |
|-------------------|----------|---------|
| `<opened_duration_de>` | German | "2 Stunden und 15 Minuten" |
| `<opened_duration_en>` | English | "2 hours and 15 minutes" |
| `<opened_duration_fr>` | French | "2 heures et 15 minutes" |
| `<opened_duration_it>` | Italian | "2 ore e 15 minuti" |

| Ordinal Variable | Language | Example (8th) |
|------------------|----------|---------------|
| `<counter_ordinal_de>` | German | "achte" |
| `<counter_ordinal_en>` | English | "eighth" |
| `<counter_ordinal_fr>` | French | "huitième" |
| `<counter_ordinal_it>` | Italian | "ottava" |

**Example - English notifications:**
```yaml
notification_title: "WARNING: <friendly_name> is open!"

notification_msg_first: |
  <friendly_name> has been open for <opened_duration_en>!
  Please check if this is intentional.

notification_msg_repeat: |
  REMINDER: <friendly_name> is still open!
  Duration: <opened_duration_en>
  This is the <counter_ordinal_en> reminder.
```

**What you'll see:**
```
WARNING: Front Door is open!
Front Door has been open for 2 hours and 15 minutes!
Please check if this is intentional.

REMINDER: Front Door is still open!
Duration: 2 hours and 15 minutes
This is the eighth reminder.
```

**No blueprint editing needed!** Just use the language variable you want in your templates.

📖 **Full guide:** [LOCALIZATION.md](https://github.com/thezeus123/OSAM/blob/main/LOCALIZATION.md)

**Example message template:**
```
WARNING: <friendly_name> has been open!

Duration: <opened_duration>
This is notification #<counter> (the <counter_ordinal>)
```

**Output:**
```
WARNING: Front Door has been open!

Duration: 2 hours and 15 minutes
This is notification #8 (the eighth)
```

## 🐛 Troubleshooting

### No notifications received?
1. Enable **Debug Mode** in blueprint
2. Check **Settings → System → Logs**
3. Filter by `osam.your_sensor_name`
4. Look for: quiet time status, temperature check, daily limit

### JSON Storage errors?
- **Using one storage per sensor?** Default 255 chars is enough!
- **Using shared storage?** Increase to 10000+ in `configuration.yaml`
- Check entity exists: `input_text.osam_[sensor]_json`
- View state to see stored JSON data
- **Best practice:** One storage per sensor (simpler!)

### Escalations not working?
* Check **Escalation After Count** is reached
* Verify **Enable Escalation** is checked
* If using **Override Quiet Time**, check it's enabled
* Look for `ESCALATION triggered` in logs (Debug Mode)

## 📝 Version History

### v3.12.1 (Current) - 2025-10-24
* 🐛 Fixed double notifications (escalation + regular)
* 🐛 Fixed YAML parsing errors from emoji characters
* ✨ Escalations now ignore daily limits properly

### v3.12.0 - 2025-10-24
* ✨ Optional escalation repeat

### v3.11.0 - 2025-10-20
* ✨ Optional quiet time (24/7 mode)
* ✨ Count-based escalation
* ✨ Escalation override quiet time
* ✨ Separate escalation volume

[Full Changelog](https://github.com/thezeus123/OSAM/blob/main/CHANGELOG.md)

## 🤝 Contributing

Found a bug? Have an idea? 
* [GitHub Issues](https://github.com/thezeus123/OSAM/issues)
* [GitHub Discussions](https://github.com/thezeus123/OSAM/discussions)

## 🔌 Notification System

OSAM supports flexible notification delivery:

### Built-in (Default)
Uses Home Assistant's `persistent_notification` - no setup needed!

### Custom Scripts (Optional)
Create your own notification script to:
- ✅ Route to multiple devices (mobile, Alexa, Telegram)
- ✅ Add rich notifications with action buttons
- ✅ Customize based on time/priority
- ✅ Integrate with third-party services

**Example:**
```yaml
notification_script: "script.zentrale_benachrichtigung"
notification_delete_script: "script.benachrichtigung_loschen"
```

Your script receives: title, message, devices, priority, volume, tag

📖 **Full guide:** [NOTIFICATION_SCRIPTS.md](https://github.com/thezeus123/OSAM/blob/main/NOTIFICATION_SCRIPTS.md)

---

## 📸 Screenshots

*(Add your screenshots here if you have them)*

## ⭐ Show Your Support

If you find OSAM useful:
* Give it a ⭐ on [GitHub](https://github.com/thezeus123/OSAM)
* Share with the community
* Submit feature requests

## 🔗 Links

* **GitHub**: https://github.com/thezeus123/OSAM
* **Documentation**: [README.md](https://github.com/thezeus123/OSAM/blob/main/README.md)
* **Changelog**: [CHANGELOG.md](https://github.com/thezeus123/OSAM/blob/main/CHANGELOG.md)

---

**OSAM** = **O**pen **S**ensor **A**lert **M**anager

Questions? Ask away! 👇
