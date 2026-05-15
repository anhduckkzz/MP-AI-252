# Clean Code TODO Notes

## 1. Remove duplicated `broker_config` assignment

**File:** `BE/MQTT_Broker/mqtt_manager.py`

Inside `MQTTManager.__init__`, `self.broker_config` is assigned twice with the same content.

Current issue:

```python
# Broker configuration
self.broker_config = {...}

# Broker configuration
self.broker_config = {...}
```

Why this should be fixed:

- It is redundant copy-paste code.
- It makes the constructor noisier.
- Future edits may accidentally update only one block and create inconsistent broker behavior.

Recommended fix:

- Keep only one `self.broker_config` block.
- Delete the duplicated second block.

---

## 2. Fix mojibake / broken Vietnamese encoding in `device_agent.py`

**File:** `BE/HERA/agents/device_agent.py`

Some Vietnamese user-facing labels are broken due to encoding issues.

Current broken examples:

```python
SENSOR_LABELS_VI = {
    "temperature": "nhiá»‡t Ä‘á»™",
    "humidity": "Ä‘á»™ áº©m",
    "light": "Ã¡nh sÃ¡ng",
    "anomaly": "Ä‘iá»ƒm báº¥t thÆ°á»ng",
}

SENSOR_UNITS = {
    "temperature": "Â°C",
    "humidity": "%",
    "light": "",
    "anomaly": "",
}
```

Recommended replacement:

```python
SENSOR_LABELS_VI = {
    "temperature": "nhiệt độ",
    "humidity": "độ ẩm",
    "light": "ánh sáng",
    "anomaly": "điểm bất thường",
}

SENSOR_UNITS = {
    "temperature": "°C",
    "humidity": "%",
    "light": "",
    "anomaly": "",
}
```

Why this should be fixed:

- These strings can appear in chatbot responses.
- Broken encoding makes Vietnamese output look unprofessional.
- It can also reduce clarity when debugging conditional sensor logic.

Extra note:

There may be other mojibake strings in the same file, especially around Vietnamese regex patterns such as `giÃ¢y`, `phÃºt`, `vÃ²ng`, etc. After fixing the labels above, scan the file for common mojibake markers like `Ã`, `Ä`, `Â`, `Æ`, `á»`, `áº`.
