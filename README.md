# OSAM - Open Sensor Alert Manager

![Version](https://img.shields.io/badge/version-3.12.1-blue.svg)
![Home Assistant](https://img.shields.io/badge/Home%20Assistant-2024.1%2B-green.svg)
![License](https://img.shields.io/badge/license-MIT-orange.svg)

Advanced monitoring system for contact sensors (doors/windows) with flexible notifications, escalation management, and extensive customization options.

## 🌟 Highlights

- 🔔 **Smart Notifications** - Customizable repeat intervals and multi-language support
- 🚨 **Escalation System** - Critical warnings after X notifications (ignores daily limits)
- 🌙 **Quiet Time** - Separate schedules for workdays/weekends (fully optional)
- ⏸️ **Snooze Function** - Counter-based notification suppression
- 🌡️ **Temperature Check** - Only notify when temperature is below threshold
- 📊 **Daily Limits** - Maximum notifications per day with midnight reset
- 🎯 **Custom Actions** - Execute actions on first/repeat/escalation/resolved
- 💾 **JSON Storage** - Unlimited sensors with persistent data
- 🐛 **Debug Mode** - Detailed logging for troubleshooting

## 📦 Installation

### Method 1: Direct Import (Recommended)

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fthezeus123%2FOSAM%2Fraw%2Fmain%2Fosam_v3.12.1.yaml)

### Method 2: Manual Installation

1. Download [`osam_v3.12.1.yaml`](https://github.com/thezeus123/OSAM/blob/main/osam_v3.12.1.yaml)
2. Copy to `/config/blueprints/automation/osam/`
3. Go to **Settings** → **Automations & Scenes** → **Blueprints**
4. Click **Import Blueprint** and select OSAM

### Method 3: Copy Blueprint URL

1. Go to **Settings** → **Automations & Scenes** → **Blueprints**
2. Click **Import Blueprint** (bottom right)
3. Paste: `https://github.com/thezeus123/OSAM/raw/main/osam_v3.12.1.yaml`

## 🔧 Setup Requirements

### Required Helper Entities (per automation)

Create these helpers in **Settings** → **Devices & Services** → **Helpers**:

1. **Input Boolean** (for repeat logic)
   - Name: `osam_[sensor_name]_helper`
   - Example: `osam_front_door_helper`

2. **Input Text** (for JSON storage)
   - Name: `osam_[sensor_name]_json` (one per sensor - **recommended**)
   - **Maximum length**: 255 (default via GUI) is sufficient for single sensor
   - **Alternative**: Use one shared storage for multiple sensors (requires 10000+ max length via YAML)

### JSON Storage Configuration Options

**Option 1: One Storage Per Sensor (Recommended)**
- Create via GUI: **Settings** → **Helpers** → **Text**
- Name: `osam_front_door_json`, `osam_bedroom_window_json`, etc.
- Max length: **255** (default) - sufficient!
- Advantage: Simple, works via GUI, no YAML editing needed

**Option 2: Shared Storage for Multiple Sensors**
- Requires manual YAML configuration in `configuration.yaml`
- Must set max length to **10000+**
- Only needed if you want one storage for ALL sensors

```yaml
# configuration.yaml - Only if using shared storage
input_text:
  osam_json_storage_shared:
    name: "OSAM JSON Storage (All Sensors)"
    max: 10000
```

**Best Practice:** Use Option 1 (one storage per sensor) unless you have 10+ sensors.

### Optional Helper Entities

3. **Input Boolean** (for snooze function)
   - Name: `osam_[sensor_name]_snooze`
   - Example: `osam_front_door_snooze`

### Optional Integrations

- **Workday Sensor**: For separate quiet time schedules (workday vs. weekend)
- **Temperature Sensor**: For temperature-based notifications
- **Weather Integration**: For weather-aware notifications (future feature)

## 📖 Quick Start Guide

### Example 1: Basic Door Monitor

Monitor a door that should be closed:

```yaml
Sensor: binary_sensor.front_door
Issue State: On (Open)
Detection Grace Period: 5 minutes
Repeat Interval: 15 minutes
```

### Example 2: Window with Temperature Check

Only notify about open window when it's cold outside:

```yaml
Sensor: binary_sensor.bedroom_window
Issue State: On (Open)
Detection Grace Period: 10 minutes
Enable Temperature Check: Yes
Temperature Threshold: 10°C
Outdoor Temperature Sensor: sensor.outdoor_temperature
```

### Example 3: Critical Door with Escalation

Front door that escalates after multiple notifications:

```yaml
Sensor: binary_sensor.front_door
Detection Grace Period: 2 minutes
Repeat Interval: 10 minutes
Enable Escalation: Yes
Escalation After Count: 6 notifications
Escalation Override Quiet Time: Yes
Escalation Alexa Volume: 80%
```

## 🎯 Use Cases

| Scenario | Configuration | Why |
|----------|--------------|-----|
| **Freezer Door** | Grace Period: 1 min, Escalation: 3 notifications | Fast response critical |
| **Front Door** | Grace Period: 5 min, Escalation: 6 notifications | Security concern |
| **Bedroom Window** | Temp Check: < 15°C, Quiet Time: Yes | Only when cold |
| **Garage Door** | Grace Period: 30 min, Daily Limit: 5 | Avoid spam |
| **Basement Window** | Issue State: Off (Closed), Repeat: 60 min | Detect break-ins |

## ⚙️ Configuration Options

### Detection Settings

- **Detection Grace Period** (0-120 min): How long sensor must be in issue state before detection
- **First Notification Delay** (0-60 min): Additional delay before first alarm
- **Close Grace Period** (0-300 sec): How long sensor must be closed before automation ends
- **Issue State**: On (Open) or Off (Closed)

### Notification Settings

- **Notification Target Devices**: Comma-separated list (e.g., `mobile_app_phone,alexa_echo`)
- **Notification Script**: Custom script or persistent_notification
- **Notification Delete Script**: Custom delete script (optional)
- **Alexa Volume**: 0-100% for standard notifications

#### Notification Templates

OSAM provides **customizable message templates** for different notification types:
- **First Notification** - When issue is first detected
- **Repeat Notification** - For subsequent reminders
- **After Quiet Time** - When quiet time ends

#### 📋 Available Template Variables

Use these variables in your notification titles and messages:

| Variable | Description | Example Output |
|----------|-------------|----------------|
| `<friendly_name>` | Display name of sensor | `Front Door` |
| `<opened_duration>` | Human-readable duration (localized) | `2 hours and 15 minutes` |
| `<counter>` | Notification count | `8` |
| `<counter_ordinal>` | Ordinal number (localized) | `eighth` |

**Example notification template:**
```yaml
notification_title: "WARNING: <friendly_name> is open!"

notification_msg_first: |
  <friendly_name> has been open for <opened_duration>!
  Please check if this is intentional.

notification_msg_repeat: |
  REMINDER #<counter>: <friendly_name> is still open!
  Duration: <opened_duration>
  This is the <counter_ordinal> reminder.
```

**Rendered output (after 8 notifications):**
```
Title: WARNING: Front Door is open!

Message:
REMINDER #8: Front Door is still open!
Duration: 2 hours and 15 minutes
This is the eighth reminder.
```

#### 🌍 Language-Specific Variables

Instead of using `<opened_duration>` and `<counter_ordinal>`, you can use language-specific Jinja variables directly:

| Variable | Language | Example |
|----------|----------|---------|
| `{{ opened_duration_DE }}` | German | `2 Stunden und 15 Minuten` |
| `{{ opened_duration_EN }}` | English | `2 hours and 15 minutes` |
| `{{ opened_duration_FR }}` | French | `2 heures et 15 minutes` |
| `{{ opened_duration_IT }}` | Italian | `2 ore e 15 minuti` |

**Example with direct language variable:**
```yaml
notification_msg_first: |
  WARNING: <friendly_name> has been open!
  Duration: {{ opened_duration_EN }}
  Notification: {{ counter_ordinal_EN }}
```

See [LOCALIZATION.md](LOCALIZATION.md) for more details on changing languages.

### Repeat Settings

- **Repeats Enabled**: Enable/disable repeated notifications
- **Repeat Interval** (1-120 min): Time between notifications

### Quiet Time (Optional)

- **Enable Quiet Time**: Can be completely disabled for 24/7 monitoring
- **Workday Start/End**: Separate times for workdays
- **Non-Workday Start/End**: Separate times for weekends/holidays
- **Workday Sensor**: Binary sensor for automatic workday detection

### Escalation System

- **Enable Escalation**: Send critical warnings after X notifications
- **Escalation After Count** (2-50): Trigger after this many notifications
- **Escalation Override Quiet Time**: Send escalations even during quiet hours
- **Repeat Escalation**: Continue sending escalations on every repeat
- **Escalation Devices**: Separate devices for critical alerts (optional)
- **Escalation Alexa Volume**: Separate volume (0-100%)

**Important**: Escalations ignore daily limits but still increment the counter!

### Snooze Function

- **Enable Snooze**: Temporarily suppress notifications
- **Snooze Helper**: Input boolean to control snooze
- **Snooze Count** (1-20): Number of notifications to suppress

### Temperature Check

- **Enable Temperature Check**: Only notify when cold
- **Temperature Sensor**: Outdoor temperature sensor
- **Temperature Threshold** (-20 to 30°C): Notify if below this value

### Daily Limit

- **Enable Notification Limit**: Cap notifications per day
- **Daily Limit** (0-50): Maximum notifications (0 = unlimited)
- **Resets**: Automatically at midnight

### Custom Actions

Execute custom actions at different stages:
- **First Notification**: E.g., turn on lights
- **Repeat Notification**: E.g., flash lights
- **Escalation**: E.g., activate siren
- **Issue Resolved**: E.g., turn off lights

### Debug Mode

Enable detailed logging to Home Assistant's system log:
```
Settings → System → Logs
```
Filter by `osam.[sensor_name]`

## 📝 Notification Message Templates

### Available Template Variables

Use these variables in your notification titles and messages:

| Variable | Description | Example Output |
|----------|-------------|----------------|
| `<friendly_name>` | Display name of sensor | `Front Door` |
| `<opened_duration>` | Duration in German (default) | `2 Stunden und 15 Minuten` |
| `<counter>` | Notification count | `8` |
| `<counter_ordinal>` | Ordinal in German (default) | `achte` |

### 🌍 Multi-Language Variables

**Want notifications in English, French, or Italian?** Simply use the language-specific variables:

#### Duration Variables (in your language)

| Variable | Language | Example Output |
|----------|----------|----------------|
| `<opened_duration_de>` | German | `2 Stunden und 15 Minuten` |
| `<opened_duration_en>` | English | `2 hours and 15 minutes` |
| `<opened_duration_fr>` | French | `2 heures et 15 minutes` |
| `<opened_duration_it>` | Italian | `2 ore e 15 minuti` |

#### Ordinal Number Variables (in your language)

| Variable | Language | Example Output |
|----------|----------|----------------|
| `<counter_ordinal_de>` | German | `achte` (eighth) |
| `<counter_ordinal_en>` | English | `eighth` |
| `<counter_ordinal_fr>` | French | `huitième` |
| `<counter_ordinal_it>` | Italian | `ottava` |

### 📋 Usage Examples

**Example 1: English Notifications**
```yaml
notification_title: "WARNING: <friendly_name> is open!"

notification_msg_first: |
  <friendly_name> has been open for <opened_duration_en>!
  Please check if this is intentional.

notification_msg_repeat: |
  REMINDER #<counter>: <friendly_name> is still open!
  Duration: <opened_duration_en>
  This is the <counter_ordinal_en> reminder.
```

**Output:**
```
Title: WARNING: Front Door is open!

Message:
REMINDER #8: Front Door is still open!
Duration: 2 hours and 15 minutes
This is the eighth reminder.
```

**Example 2: French Notifications**
```yaml
notification_msg_first: |
  ATTENTION: <friendly_name> est ouvert!
  Durée: <opened_duration_fr>
  C'est la <counter_ordinal_fr> notification.
```

**Output:**
```
ATTENTION: Front Door est ouvert!
Durée: 2 heures et 15 minutes
C'est la huitième notification.
```

**Example 3: Mixed Languages (Different Automations)**
```yaml
# Front Door - English
notification_msg_first: |
  WARNING: <friendly_name> has been open for <opened_duration_en>!

# Kitchen Window - German
notification_msg_first: |
  WARNUNG: <friendly_name> ist seit <opened_duration_de> offen!

# Bedroom Window - French
notification_msg_first: |
  ATTENTION: <friendly_name> est ouvert depuis <opened_duration_fr>!
```

### 💡 Tips

- **No blueprint editing needed!** Just use the language variable you want
- **Mix languages**: Each automation can use a different language
- **Default variables**: `<opened_duration>` = `<opened_duration_de>` and `<counter_ordinal>` = `<counter_ordinal_de>`
- **Write your text in your language**: Only the duration and ordinal are provided in multiple languages - you still write "WARNING:" or "ATTENTION:" yourself

For more language details, see [LOCALIZATION.md](LOCALIZATION.md).

**First Notification:**
```
WARNING: <friendly_name> has been open!

Open for: <opened_duration>
Notification #<counter>
```

**Repeat Notification:**
```
REMINDER: <friendly_name> is still open!

Duration: <opened_duration>
This is the <counter_ordinal> reminder.
```

**Escalation:**
```
🚨 CRITICAL: <friendly_name> open!

Duration: <opened_duration>
Total notifications: <counter>

Please close immediately!
```

## 🔄 Version History

### V3.12.1 (2025-10-24) - BUGFIX
- ✅ Fixed double notifications (escalation + regular were both sent)
- ✅ Escalations now ignore daily limit but still increment counter
- ✅ Removed emoji characters (fixed YAML parsing errors)

### V3.12 (2025-10-24)
- ✨ Optional escalation repeat (continue sending on every repeat)

### V3.11 (2025-10-20)
- ✨ Quiet Time is now optional (can be disabled for 24/7)
- ✨ Escalation based on notification count instead of time
- ✨ Escalation can override Quiet Time
- ✨ Separate Alexa volume for escalation notifications

## 🛠️ Troubleshooting

### "Helper entity not found"
Make sure you created the required input_boolean and input_text helpers.

### "JSON storage error"
**Symptom:** Error messages about JSON parsing or storage

**Solution:**
1. **Using one storage per sensor (recommended)?** → 255 chars (default) is sufficient
2. **Using shared storage for multiple sensors?** → Increase max length to 10000+ in YAML:
   ```yaml
   # configuration.yaml
   input_text:
     osam_json_storage_shared:
       name: "OSAM Shared Storage"
       max: 10000
   ```
3. Check entity exists and is spelled correctly
4. View entity state in Developer Tools to see stored JSON

**Best Practice:** Use separate JSON storage for each sensor to avoid this issue entirely.

### "No notifications received"
1. Enable Debug Mode
2. Check Home Assistant logs
3. Verify notification script/service exists
4. Check if in Quiet Time period
5. Check if Daily Limit reached

### "Notifications during Quiet Time"
- Check if Escalation is enabled with "Override Quiet Time"
- Verify Workday Sensor state if using separate schedules
- Confirm Quiet Time is enabled

### "Counter not incrementing"
- Check JSON storage is working (view entity state)
- Verify input_text has sufficient max length
- Enable Debug Mode to see detailed logs

## 🤝 Contributing

Contributions are welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Test thoroughly
4. Submit a pull request

## 📜 License

MIT License - See [LICENSE](LICENSE) file for details

## 🔌 Custom Notification Scripts (Optional)

OSAM can use your own notification scripts for maximum flexibility. The default configuration uses Home Assistant's built-in `persistent_notification`, but you can create custom scripts to:

- Route to multiple devices (mobile apps, Alexa, Telegram, etc.)
- Add custom logic (time-based, priority-based)
- Create rich notifications with actions and images
- Integrate with third-party services

### Quick Start

**Default (No Custom Script Needed):**
```yaml
notification_script: ""  # Uses persistent_notification
notification_delete_script: ""  # Uses persistent_notification.dismiss
```

**With Custom Script:**
```yaml
notification_script: "script.zentrale_benachrichtigung"
notification_delete_script: "script.benachrichtigung_loschen"
```

### Required Script Parameters

If you create a custom notification script, it must accept:
- `devices` (string) - Comma-separated device list
- `titel` (string) - Notification title
- `nachricht` (string) - Notification message
- `vorlage` (string) - Template type (`default` or `alarm`)
- `priority` (string) - Priority level (`normal` or `high`)
- `alexa_lautstaerke` (float) - Alexa volume (0.0-1.0)
- `tag` (string) - Notification ID for updates/deletion

### Full Documentation

See **[NOTIFICATION_SCRIPTS.md](NOTIFICATION_SCRIPTS.md)** for:
- Complete script structure examples
- Integration patterns for different notification systems
- Advanced features (rich notifications, multi-language, etc.)
- Troubleshooting guide

## 🔗 Links

- **GitHub Repository**: https://github.com/thezeus123/OSAM
- **Home Assistant Community**: [Post your issues and suggestions](https://community.home-assistant.io/c/blueprints-exchange/53)
- **Bug Reports**: [GitHub Issues](https://github.com/thezeus123/OSAM/issues)
- **FAQ**: [FAQ.md](FAQ.md) - Frequently Asked Questions
- **Notification Scripts Guide**: [NOTIFICATION_SCRIPTS.md](NOTIFICATION_SCRIPTS.md)
- **Localization Guide**: [LOCALIZATION.md](LOCALIZATION.md) - Change language (DE/EN/FR/IT)

## 💬 Support

- 🐛 **Bug Reports**: [GitHub Issues](https://github.com/thezeus123/OSAM/issues)
- 💡 **Feature Requests**: [GitHub Discussions](https://github.com/thezeus123/OSAM/discussions)
- 💬 **Community Support**: [Home Assistant Forum](https://community.home-assistant.io/)

## ⭐ Show Your Support

If you find OSAM useful, please give it a ⭐ on GitHub!

---

**OSAM** = **O**pen **S**ensor **A**lert **M**anager

Made with ❤️ for the Home Assistant community
