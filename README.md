## Gesture & Mobile Remote Mouse Controller

This project turns your Windows PC into a mouse that can be controlled in two ways:

- **Hand‑gesture virtual mouse** using your webcam (`main.py`)
- **Mobile phone touchpad/remote** over Wi‑Fi in a browser (`mobile_remote.py`)

Both approaches use `pyautogui` to move and click the real system cursor.

---

## Features

- **Webcam hand‑gesture control (`main.py`)**
  - Move the mouse cursor by moving your index finger in front of the camera
  - **Left click** with thumb + index pinch
  - **Right click** with index + middle pinch
  - **Scrolling** by moving a closed fist up and down
  - Basic smoothing, sensitivity, and frame‑reduction settings for better control

- **Mobile remote control (`mobile_remote.py`)**
  - Simple web page that turns your phone into a touchpad
  - Drag on the touch area to move the cursor
  - Dedicated buttons for **left** and **right** click
  - Works over your local network (phone and PC on the same Wi‑Fi)

---

## Requirements

**Platform**
- Windows 10+ (project calls `ctypes.windll.user32.SetProcessDPIAware()` and uses `pyautogui`)

**Python**
- Python 3.9+ (3.10/3.11 also fine)

**Python packages**

You can install all dependencies with:

```bash
pip install opencv-python numpy pyautogui cvzone flask
```

Packages used:
- **OpenCV (`opencv-python`)**: Webcam capture and display window
- **NumPy**: Coordinate math and interpolation
- **cvzone**: `HandDetector` for finger and hand landmark detection
- **pyautogui**: Moving the cursor, clicks, scroll
- **Flask**: Simple web server for the mobile remote page

> **Note**: `pyautogui` may require additional OS permissions to control the mouse. On Windows, usually none beyond normal app permissions are required, but some antivirus / security tools might need to whitelist Python.

---

## Running the Hand‑Gesture Virtual Mouse (`main.py`)

1. **Connect a webcam** to your PC (or use the built‑in one).
2. Make sure the dependencies are installed:

   ```bash
   pip install opencv-python numpy pyautogui cvzone
   ```

3. Run the script from the project directory:

   ```bash
   python main.py
   ```

4. A window titled **"Virtual Mouse"** will appear showing your camera feed.

### Gesture controls

- **Cursor movement**
  - Raise only your **index finger**: fingers state `[0,1,0,0,0]`
  - Move your finger within the camera frame to move the cursor.

- **Left click**
  - Raise **thumb + index** (others down): `[1,1,0,0,0]`
  - Pinch the thumb and index tip together; a small distance between tips triggers a left click.

- **Right click**
  - Raise **index + middle** (others down): `[0,1,1,0,0]`
  - Bring the two fingertips close to trigger a right click.

- **Scroll**
  - Make a **closed fist**: `[0,0,0,0,0]`
  - Move the fist **up** to scroll up, **down** to scroll down.

### Tuning movement behavior

In `main.py`:

- `frame_reduction`: Shrinks the active region inside the camera frame.
  - **Smaller** value → **more reach** but less precision near edges.
- `sensitivity`: Multiplies the mapped screen coordinates.
  - **Higher** value → cursor moves **faster**.
- `smoothening`: Used in `smooth_coordinates`.
  - **Lower** value → cursor reacts **faster** but can be jittery.
  - **Higher** value → smoother but more laggy movement.

Experiment with these three values to match your screen size, camera position, and preference.

### Exiting

- Focus the OpenCV window and press **`q`** on the keyboard to quit.

---

## Running the Mobile Remote (`mobile_remote.py`)

This mode lets you control the PC mouse from any device with a browser (phone or tablet) on the same local network.

1. Install dependencies:

   ```bash
   pip install flask pyautogui
   ```

2. Start the Flask server:

   ```bash
   python mobile_remote.py
   ```

3. The script will print something like:

   ```text
   Starting server...
   * Running on http://0.0.0.0:5000
   ```

4. Find your PC’s local IP address, for example:
   - On Windows PowerShell:

     ```bash
     ipconfig
     ```

   - Look for something like `IPv4 Address . . . : 192.168.1.23`.

5. On your **phone**, open a browser and visit:

   ```text
   http://<PC_IP_ADDRESS>:5000
   ```

   Example: `http://192.168.1.23:5000`

6. You’ll see a page with:
   - A large **touchpad area**
   - **Left Click** and **Right Click** buttons

### Mobile controls

- **Move cursor**
  - Drag your finger on the touchpad area; relative `dx/dy` will move the PC cursor (`moveRel`).
- **Left click**
  - Tap the **Left Click** button.
- **Right click**
  - Tap the **Right Click** button.

### Security notes

- The server binds to `0.0.0.0` (all interfaces) on port `5000`, so:
  - Anyone on the same network who knows your IP and port can control your mouse.
- Run this only on **trusted local networks**.
- Close the script / terminal when you’re done to stop the server.

---

## Known Limitations & Tips

- **Lighting & background** heavily affect the hand‑gesture detector; try to use:
  - Good, even lighting
  - A clean background behind your hand
- **Single‑hand only**: `main.py` is configured with `maxHands=1`.
- **Gesture misfires** can still happen; there is a small **gesture cooldown** (`GESTURE_DELAY`) to reduce accidental double‑clicks.
- **High‑DPI screens**: `SetProcessDPIAware()` is called to improve cursor alignment.
- **Performance**:
  - If the webcam feed is laggy, close other apps using the camera or reduce resolution.

---

## Project Structure

- `main.py` – Webcam‑based hand‑gesture virtual mouse
- `mobile_remote.py` – Flask server and HTML/JS mobile remote touchpad

---

## Future Improvements (Ideas)

- Add more gestures (drag, middle‑click, volume control)
- Add a simple GUI to switch modes and adjust sensitivity live
- Use HTTPS and simple PIN auth for the mobile remote
- Package as a standalone executable for easier distribution on Windows

