# OSAM - Important Links Reference

## 🔗 Installation Links

### Direct Import Link (One-Click)
```
https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fthezeus123%2FOSAM%2Fraw%2Fmain%2Fosam_v3.12.1.yaml
```

### Badge for Markdown (README/Forum)
```markdown
[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fthezeus123%2FOSAM%2Fraw%2Fmain%2Fosam_v3.12.1.yaml)
```

### Raw Blueprint URL (Manual Import)
```
https://github.com/thezeus123/OSAM/raw/main/osam_v3.12.1.yaml
```

### Blueprint GitHub Page
```
https://github.com/thezeus123/OSAM/blob/main/osam_v3.12.1.yaml
```

## 📚 Documentation Links

### Main Documentation
- **README**: https://github.com/thezeus123/OSAM/blob/main/README.md
- **CHANGELOG**: https://github.com/thezeus123/OSAM/blob/main/CHANGELOG.md
- **FAQ**: https://github.com/thezeus123/OSAM/blob/main/FAQ.md

### Guides
- **Notification Scripts**: https://github.com/thezeus123/OSAM/blob/main/NOTIFICATION_SCRIPTS.md
- **Localization**: https://github.com/thezeus123/OSAM/blob/main/LOCALIZATION.md
- **Contributing**: https://github.com/thezeus123/OSAM/blob/main/CONTRIBUTING.md

### Examples
- **Basic Door**: https://github.com/thezeus123/OSAM/blob/main/examples/basic_door.md
- **Window + Temperature**: https://github.com/thezeus123/OSAM/blob/main/examples/window_temperature.md
- **Critical Door + Escalation**: https://github.com/thezeus123/OSAM/blob/main/examples/critical_door_escalation.md

## 🏠 Community Links

### GitHub
- **Repository**: https://github.com/thezeus123/OSAM
- **Issues**: https://github.com/thezeus123/OSAM/issues
- **Discussions**: https://github.com/thezeus123/OSAM/discussions
- **Releases**: https://github.com/thezeus123/OSAM/releases

### Home Assistant Community
- **Blueprints Exchange**: https://community.home-assistant.io/c/blueprints-exchange/53
- **OSAM Thread**: [Will be created after posting]

## 🎯 Quick Reference for Users

### Installation Command
Tell users to paste this URL in Blueprint Import:
```
https://github.com/thezeus123/OSAM/raw/main/osam_v3.12.1.yaml
```

### One-Click Install
Send users this link:
```
https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fthezeus123%2FOSAM%2Fraw%2Fmain%2Fosam_v3.12.1.yaml
```

## 📝 Notes for Future Updates

When releasing a new version (e.g., v3.13.0):

1. **Update blueprint filename** in all links:
   - Change `osam_v3.12.1.yaml` to `osam_v3.13.0.yaml`

2. **Generate new import link**:
   ```python
   import urllib.parse
   url = "https://github.com/thezeus123/OSAM/raw/main/osam_v3.13.0.yaml"
   encoded = urllib.parse.quote(url, safe='')
   print(f"https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url={encoded}")
   ```

3. **Update in these files**:
   - README.md (Installation section)
   - FORUM_POST.md (Installation section)
   - Any other docs referencing the blueprint

## ⚠️ Important: Use `/raw/` not `/blob/`

**Correct** (for import):
```
https://github.com/thezeus123/OSAM/raw/main/osam_v3.12.1.yaml
```

**Incorrect** (for viewing only):
```
https://github.com/thezeus123/OSAM/blob/main/osam_v3.12.1.yaml
```

The `/raw/` path provides the actual YAML file content that Home Assistant can import.
The `/blob/` path is for viewing the file in GitHub's web interface.

---

**Generated**: 2024-10-27
**Version**: v3.12.1
