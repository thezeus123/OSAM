# Changelog

All notable changes to OSAM (Open Sensor Alert Manager) will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [3.12.1] - 2024-10-24

### Fixed
- **Critical**: Fixed double notifications bug where both escalation and regular notifications were sent simultaneously
- **Critical**: Fixed YAML parsing errors caused by emoji characters (U+008F and similar)
- Escalations now properly ignore daily limit while still incrementing the notification counter

### Changed
- Replaced emoji characters in default notification templates with text equivalents
  - Warning emoji (⚠️) → "WARNUNG:" / "WARNING:"
  - Alert emoji (🚨) → "KRITISCH:" / "CRITICAL:"

### Technical Details
- Modified repeat_check trigger to use `if-else` block instead of sequential execution
- Escalation notifications now run in `then` block
- Regular notifications now run in `else` block (only when NO escalation)
- Added counter increment after escalation (previously missing)
- Removed all Unicode control characters that could cause parsing issues

## [3.12.0] - 2024-10-18

### Added
- **Escalation Repeat**: New option to continue sending escalation notifications on every repeat cycle
  - Previously: Escalation sent only once
  - Now: Optional repeated escalations for critical situations
- New input parameter: `escalation_repeat_enabled` (default: false)

### Changed
- Escalation flag `escalation_sent` now only set when repeat is disabled
- Improved escalation logic for repeated critical warnings

## [3.11.0] - 2024-09-28

### Added
- **Optional Quiet Time**: Quiet Time can now be completely disabled for 24/7 monitoring
  - New parameter: `enable_quiet_time` (default: true)
  - When disabled, notifications are sent around the clock
- **Count-based Escalation**: Escalations now trigger after X notifications instead of time duration
  - More predictable and easier to configure
  - New parameter: `escalation_after_count` (replaces time-based trigger)
- **Escalation Override Quiet Time**: Escalations can now be sent even during quiet hours
  - New parameter: `escalation_override_quiet_time` (default: true)
  - Critical alerts no longer blocked by quiet time
- **Separate Escalation Volume**: Different Alexa volume for escalation notifications
  - New parameter: `escalation_alexa_volume` (default: 80%)
  - Normal notifications can be quieter, escalations louder

### Changed
- Improved escalation logic with clearer conditional checks
- Enhanced quiet time calculation to support optional mode
- Escalation conditions now check both quiet time override and current period

### Deprecated
- Time-based escalation trigger (replaced by count-based system)

## [3.10.0] - 2024-09-05

### Added
- **Multi-language duration support**: Added French (FR) and Italian (IT) translations
  - Variables: `opened_duration_FR`, `opened_duration_IT`
  - Existing: `opened_duration_DE`, `opened_duration_EN`
- **Custom Actions**: Four customizable action hooks
  - `custom_action_on_send_notification` - First notification sent
  - `custom_action_repeat_notification` - Repeat notification sent
  - `custom_action_escalation` - Escalation triggered
  - `custom_action_from_issue_state` - Issue resolved
- **Escalation System**: Critical warnings after prolonged open state
  - Separate notification devices for escalations
  - Custom escalation messages and titles
  - Escalation tracking in JSON storage

### Changed
- Improved JSON storage structure with escalation tracking
- Enhanced notification counter with escalation flag
- Better handling of helper entity state changes

## [3.9.0] - 2024-08-12

### Added
- **Daily Notification Limit**: Cap the number of notifications per day
  - Automatic reset at midnight
  - Optional feature (can be disabled)
  - Prevents notification spam
- **Temperature-based Notifications**: Only send alerts when temperature is below threshold
  - Integration with outdoor temperature sensors
  - Configurable threshold (-20 to 30°C)
  - Ideal for "window open when cold" alerts

### Changed
- Improved condition checking for notification sending
- Enhanced variable calculation for temperature and limit checks

## [3.8.0] - 2024-07-22

### Added
- **Counter-based Snooze**: Suppress X number of notifications instead of time-based
  - More predictable snooze behavior
  - Automatic snooze deactivation after counter reaches zero
  - Snooze counter tracking in JSON storage
- **Snooze Counter Display**: Track remaining snoozed notifications
- **Automatic Snooze Disable**: Snooze helper automatically turns off when counter reaches zero

### Changed
- Refactored snooze logic to use counter instead of timestamp
- Improved snooze remaining calculation
- Enhanced debug logging for snooze operations

### Fixed
- Snooze helper not resetting properly
- Snooze counter initialization issues

## [3.7.0] - 2024-06-30

### Added
- **JSON Storage System**: Persistent data storage for unlimited sensors
  - Single input_text entity can manage multiple sensors
  - Stores: counter, first_opened, helper_active, repeat_timer, escalation_sent
  - Replaces individual helper entities per sensor
- **Workday Integration**: Separate quiet time schedules for workdays vs. non-workdays
  - Optional workday sensor integration
  - Different start/end times for weekdays and weekends
- **Notification History**: Track notification count per sensor per day

### Changed
- Major refactoring from individual helpers to JSON storage
- Improved scalability for multiple sensors
- Enhanced data persistence across restarts

### Migration Guide
- Existing automations need to add `json_storage` input_text entity
- Old counter helpers can be removed after migration
- Data will be automatically migrated on first trigger

## [3.6.0] - 2024-06-08

### Added
- **Ordinal Counter**: Display notification number as ordinal (first, second, third...)
  - New variable: `<counter_ordinal>`
  - Supports German, English, French, Italian
  - Up to 20th notification, then numeric (21st, 22nd...)
- **Enhanced Notification Templates**: More variables for message customization
  - `<counter>` - Numeric count
  - `<counter_ordinal>` - First, second, third...
  - `<opened_duration>` - Human-readable duration
  - `<friendly_name>` - Sensor display name

### Changed
- Improved notification message structure
- Enhanced template variable replacement logic

## [3.5.0] - 2024-05-15

### Added
- **Quiet Time**: Define periods when notifications are suppressed
  - Configurable start and end times
  - Notifications resume automatically after quiet time
  - Separate message for "after quiet time" notifications
- **Notification After Silence**: Special notification when quiet time ends
  - Reminds about still-open sensors
  - Configurable message template

### Changed
- Added silence period calculation logic
- Improved time-based trigger handling

## [3.4.0] - 2024-04-20

### Added
- **Repeat Notifications**: Configurable repeat interval
  - Send periodic reminders while sensor remains open
  - Adjustable interval (1-120 minutes)
  - Can be disabled entirely
- **Notification Counter**: Track how many times notified about same event
  - Stored in sensor attributes
  - Resets when sensor closes
  - Available as variable in notification templates

### Changed
- Enhanced helper entity logic for repeat timing
- Improved state tracking

## [3.3.0] - 2024-03-28

### Added
- **First Notification Delay**: Additional delay after detection
  - Grace period + First notification delay = Total time before alert
  - Useful for brief checks (e.g., getting mail)
- **Delete Notification on Close**: Automatically dismiss notification when sensor closes
  - Keeps notification panel clean
  - Optional feature (can be disabled)

### Changed
- Restructured timing logic for delays
- Improved notification lifecycle management

## [3.2.0] - 2024-03-05

### Added
- **Multi-language Support**: Duration formatting in multiple languages
  - German (DE) - default
  - English (EN)
  - Automatic pluralization rules per language
- **Close Grace Period**: Delay before considering sensor "closed"
  - Prevents false "resolved" for momentary closures
  - Configurable from 0-300 seconds

### Changed
- Enhanced duration calculation with language-specific formatting
- Improved resolved state detection

## [3.1.0] - 2024-02-14

### Added
- **Custom Notification Scripts**: Use your own notification system
  - Support for custom scripts (e.g., Alexa TTS, Telegram)
  - Fallback to persistent_notification
  - Configurable Alexa volume
- **Friendly Name Override**: Custom display names for sensors
  - Falls back to entity friendly_name if not set
- **Notification Templates**: Customizable title and message templates

### Changed
- Refactored notification system to support multiple delivery methods
- Improved notification service detection

## [3.0.0] - 2024-01-22

### Added
- **Detection Grace Period**: Delay before considering sensor "open"
  - Prevents false alarms from brief door openings
  - Configurable from 0-120 minutes
- **Issue State Selection**: Choose between "On" (open) or "Off" (closed) as problem state
  - Flexibility for different sensor types
  - Default: "On" for typical door/window sensors
- **Debug Mode**: Detailed logging for troubleshooting
  - Logs to Home Assistant system log
  - Includes state changes, conditions, and decisions

### Changed
- Complete rewrite from version 2.x
- Improved reliability and maintainability
- Better separation of concerns

### Breaking Changes
- Configuration structure changed significantly
- Migration required from v2.x
- Helper entities now required (input_boolean)

## [2.0.0] - 2023-11-18

### Added
- Basic repeat functionality
- Simple notification system
- Configurable sensor selection

### Changed
- Improved from initial prototype
- Basic timing controls

## [1.0.0] - 2023-10-05

### Added
- Initial release
- Basic open/closed detection
- Single notification on state change

---

## Legend

- **Added**: New features
- **Changed**: Changes in existing functionality
- **Deprecated**: Soon-to-be removed features
- **Removed**: Removed features
- **Fixed**: Bug fixes
- **Security**: Security fixes
