# OSAM Localization Guide

## 🌍 Built-in Multi-Language Support

OSAM supports **4 languages** out of the box - **no configuration needed!**

Simply use the language-specific variables in your notification templates:

- 🇩🇪 German
- 🇬🇧 English  
- 🇫🇷 French
- 🇮🇹 Italian

## 📋 Available Language Variables

### Duration Variables

| Variable | Language | Example Output |
|----------|----------|----------------|
| `<opened_duration_de>` | German | `2 Stunden und 15 Minuten` |
| `<opened_duration_en>` | English | `2 hours and 15 minutes` |
| `<opened_duration_fr>` | French | `2 heures et 15 minutes` |
| `<opened_duration_it>` | Italian | `2 ore e 15 minuti` |

### Ordinal Number Variables

| Variable | Language | 1st | 2nd | 3rd | 8th |
|----------|----------|-----|-----|-----|-----|
| `<counter_ordinal_de>` | German | erste | zweite | dritte | achte |
| `<counter_ordinal_en>` | English | first | second | third | eighth |
| `<counter_ordinal_fr>` | French | première | deuxième | troisième | huitième |
| `<counter_ordinal_it>` | Italian | prima | seconda | terza | ottava |

## 🎯 How to Use

### Example 1: English Notifications

```yaml
notification_title: "WARNING: <friendly_name> is open!"

notification_msg_first: |
  <friendly_name> has been open for <opened_duration_en>!
  Please check if this is intentional.

notification_msg_repeat: |
  REMINDER #<counter>: <friendly_name> is still open!
  Duration: <opened_duration_en>
  This is the <counter_ordinal_en> reminder.

escalation_title: "CRITICAL: <friendly_name> open!"

escalation_message: |
  <friendly_name> has been open for <opened_duration_en>!
  This is the <counter_ordinal_en> notification - please close immediately!
```

**Output:**
```
WARNING: Front Door is open!
Front Door has been open for 2 hours and 15 minutes!
Please check if this is intentional.

REMINDER #8: Front Door is still open!
Duration: 2 hours and 15 minutes
This is the eighth reminder.
```

### Example 2: French Notifications

```yaml
notification_title: "ATTENTION: <friendly_name> est ouvert!"

notification_msg_first: |
  <friendly_name> est ouvert depuis <opened_duration_fr>!
  Veuillez vérifier si c'est intentionnel.

notification_msg_repeat: |
  RAPPEL #<counter>: <friendly_name> est toujours ouvert!
  Durée: <opened_duration_fr>
  C'est le <counter_ordinal_fr> rappel.
```

**Output:**
```
ATTENTION: Front Door est ouvert!
Front Door est ouvert depuis 2 heures et 15 minutes!
Veuillez vérifier si c'est intentionnel.

RAPPEL #8: Front Door est toujours ouvert!
Durée: 2 heures et 15 minutes
C'est le huitième rappel.
```

### Example 3: Italian Notifications

```yaml
notification_title: "ATTENZIONE: <friendly_name> è aperto!"

notification_msg_first: |
  <friendly_name> è aperto da <opened_duration_it>!
  Controlla se è intenzionale.

notification_msg_repeat: |
  PROMEMORIA #<counter>: <friendly_name> è ancora aperto!
  Durata: <opened_duration_it>
  Questo è il <counter_ordinal_it> promemoria.
```

**Output:**
```
ATTENZIONE: Front Door è aperto!
Front Door è aperto da 2 ore e 15 minuti!
Controlla se è intenzionale.

PROMEMORIA #8: Front Door è ancora aperto!
Durata: 2 ore e 15 minuti
Questo è l'ottavo promemoria.
```

### Example 4: German Notifications (Default)

```yaml
notification_title: "WARNUNG: <friendly_name> ist offen!"

notification_msg_first: |
  <friendly_name> ist seit <opened_duration_de> offen!
  Bitte prüfen Sie, ob dies beabsichtigt ist.

notification_msg_repeat: |
  ERINNERUNG #<counter>: <friendly_name> ist immer noch offen!
  Dauer: <opened_duration_de>
  Dies ist die <counter_ordinal_de> Erinnerung.
```

## 🔄 Mix Languages Across Automations

Each automation can use a different language:

**Front Door (English):**
```yaml
notification_msg_first: |
  WARNING: <friendly_name> has been open for <opened_duration_en>!
```

**Kitchen Window (German):**
```yaml
notification_msg_first: |
  WARNUNG: <friendly_name> ist seit <opened_duration_de> offen!
```

**Bedroom Window (French):**
```yaml
notification_msg_first: |
  ATTENTION: <friendly_name> est ouvert depuis <opened_duration_fr>!
```

**Garage Door (Italian):**
```yaml
notification_msg_first: |
  ATTENZIONE: <friendly_name> è aperto da <opened_duration_it>!
```

## 💡 Important Notes

### Default Variables

For backwards compatibility, these default variables exist:
- `<opened_duration>` = `<opened_duration_de>` (German)
- `<counter_ordinal>` = `<counter_ordinal_de>` (German)

**Recommendation:** Use language-specific variables (e.g., `<opened_duration_en>`) to be explicit.

### You Still Write Your Own Text

The language variables only provide:
- **Duration** (e.g., "2 hours and 15 minutes")
- **Ordinal numbers** (e.g., "eighth")

You still need to write:
- Your notification titles (e.g., "WARNING:", "ATTENTION:", "WARNUNG:")
- Your notification messages (e.g., "Please close immediately!")
- All other text in your preferred language

### No Configuration Required

**You do NOT need to:**
- ❌ Edit the blueprint
- ❌ Change any YAML configuration
- ❌ Restart Home Assistant
- ❌ Re-import anything

**You ONLY need to:**
- ✅ Use the language variable you want (e.g., `<opened_duration_en>`)

## 📊 Duration Format Examples

| Duration | DE | EN | FR | IT |
|----------|----|----|----|----|
| 5 minutes | 5 Minuten | 5 minutes | 5 minutes | 5 minuti |
| 1 hour | 1 Stunde | 1 hour | 1 heure | 1 ora |
| 2 hours 15 min | 2 Stunden und 15 Minuten | 2 hours and 15 minutes | 2 heures et 15 minutes | 2 ore e 15 minuti |
| 1 day 3 hours | 1 Tag und 3 Stunden | 1 day and 3 hours | 1 jour et 3 heures | 1 giorno e 3 ore |
| 2 days 5 hours 30 min | 2 Tage und 5 Stunden und 30 Minuten | 2 days and 5 hours and 30 minutes | 2 jours et 5 heures et 30 minutes | 2 giorni e 5 ore e 30 minuti |

## ❓ FAQ

### Q: Which language should I use?

**A:** Use whatever language your household speaks! Each automation can use a different language if needed.

### Q: Can I mix languages in one notification?

**A:** Yes, but it might look odd:

```yaml
notification_msg_first: |
  WARNING: <friendly_name> ist seit <opened_duration_en> offen!
```

Output: "WARNING: Front Door ist seit 2 hours and 15 minutes offen!"

### Q: What if I want Spanish/Dutch/etc.?

**A:** Currently only DE/EN/FR/IT are built-in. You can request additional languages on [GitHub](https://github.com/thezeus123/OSAM/issues) or contribute them yourself!

### Q: Do I need to change anything in the blueprint?

**A:** No! The language variables are already there. Just use them in your notification templates.

### Q: Will this work with existing automations?

**A:** Yes! Your existing automations will continue working. When you want to change the language, just edit the notification templates to use the new variable (e.g., change `<opened_duration>` to `<opened_duration_en>`).

## 🌐 Contributing Translations

Want to add support for your language? We'd love your contribution!

**What's needed:**
1. Duration calculation with proper pluralization rules
2. Ordinal numbers (1st-20th in your language)
3. Testing with various durations

Submit a pull request on [GitHub](https://github.com/thezeus123/OSAM) or open an issue to discuss!

---

**Questions?** Ask in the [Home Assistant Community Forum](https://community.home-assistant.io/c/blueprints-exchange/53)
