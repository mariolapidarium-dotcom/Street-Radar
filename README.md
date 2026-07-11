# 🛣️ Street-Radar — Road Hazard Detection Prototype

Detect potholes and road anomalies using your phone's accelerometer. Share findings on a live collaborative map.

## 🚀 Quick Start

### **Enable GitHub Pages (REQUIRED)**
1. Go to repository **Settings** → scroll to **Pages**
2. Under "Build and deployment":
   - Source: `Deploy from a branch`
   - Branch: `main` / `/ (root)`
3. Click **Save**
4. ⏳ Wait 1-2 minutes for deployment
5. **Access your app** at: `https://mariolapidarium-dotcom.github.io/Street-Radar/`

### **Local Testing** (no GitHub Pages needed)
```bash
# Option 1: Python HTTP server
python -m http.server 8000
# Open: http://localhost:8000

# Option 2: VS Code Live Server
# Install "Live Server" extension, right-click index.html → "Open with Live Server"
```

## 📱 How It Works

1. **Open the app** on your mobile device
2. Tap **"Započni praćenje"** (Start Tracking)
3. Grant GPS & motion sensor permissions
4. **Drive/walk** over rough surfaces (potholes, speed bumps)
5. The app detects vibrations and marks anomalies on the map
6. **Nearby detections cluster** automatically (within 20m)
7. **Warnings trigger** when you approach a known hazard (60m radius)

## 🧪 Testing Without Road Bumps

Use the **Test Helper** to inject fake anomalies:

1. Open: `test-helper.html` (served locally or via GitHub Pages)
2. Enter coordinates and confirmation level
3. Click "Add Anomaly"
4. Open main app → anomalies appear instantly

## ⚙️ Settings

- **Sensitivity Slider** (1-10):
  - Low (1-3): Only severe vibrations
  - Medium (5): Balanced detection (default)
  - High (8-10): Detects minor vibrations
- **Geolocation**: Real-time GPS tracking
- **Motion Sensor**: Accelerometer-based detection

## 💾 Data Storage

- **Storage**: Browser localStorage (persistent across sessions)
- **Expiry**: Anomalies auto-remove after 3 days without re-detection
- **Sync**: Optional backend sync (falls back to localStorage if offline)
- **Clustering**: Anomalies within 20m merge into single entry

## 📊 What You'll See

| UI Element | Meaning |
|-----------|---------|
| 🟢 Green dot (GPS) | GPS acquired |
| 🟢 Green dot (SENZOR) | Motion sensor active |
| 🔵 Blue marker | Your current location |
| 🔴 Red circles | Detected anomalies (size = severity) |
| ⚠️ Warning banner | Hazard within 60m, phone vibrates |
| 📊 Stats | Your detections + total nearby |
| 📝 List | Recent anomalies with timestamps |

## 🛠️ Development

- **index.html** — Main app (all-in-one, ~850 lines)
- **test-helper.html** — Utility for injecting test data
- **No build step required** — Pure HTML5/JavaScript/Leaflet

## 📋 Troubleshooting

| Issue | Solution |
|-------|----------|
| GitHub Pages 404 | Check Settings → Pages, ensure `main` branch + `/ (root)` selected |
| GPS not working | Mobile only; desktop needs geolocation spoofing |
| Motion detection silent | Check phone has accelerometer; tap "Započni praćenje" first |
| Data persists after refresh | Intentional! localStorage survives page reloads |
| Test anomalies not showing | Open test-helper, add data, then refresh main app |

---

**Happy testing! 🚗💨**
