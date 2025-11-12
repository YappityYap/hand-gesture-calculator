# 🖐️ Hand Gesture Calculator

A real-time calculator controlled entirely by hand gestures using computer vision and machine learning. Perform mathematical operations simply by showing different finger combinations to your webcam!

![Python](https://img.shields.io/badge/Python-3.7+-blue.svg)
![OpenCV](https://img.shields.io/badge/OpenCV-4.0+-green.svg)
![MediaPipe](https://img.shields.io/badge/MediaPipe-Latest-orange.svg)

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [How It Works](#how-it-works)
- [Requirements](#requirements)
- [Installation](#installation)
- [Usage](#usage)
- [Gesture Mapping](#gesture-mapping)
- [Technical Details](#technical-details)
- [Troubleshooting](#troubleshooting)
- [Performance Optimization](#performance-optimization)
- [Credits](#credits)
- [License](#license)

## 🎯 Overview

This project implements a gesture-based calculator that uses your webcam to detect hand gestures and perform mathematical calculations in real-time. Using Google's MediaPipe for hand tracking and OpenCV for video processing, the application recognizes different finger combinations to input numbers and operators.

## ✨ Features

- **Real-time Hand Detection**: Uses MediaPipe's advanced hand tracking with up to 2 hands simultaneously
- **Gesture Recognition**: Recognizes 0-9 digits and mathematical operators (+, -, *, /)
- **Dual Hand Support**: 
  - Right hand for numbers (0-9)
  - Left hand for operators and commands
- **Stable Gesture Detection**: Built-in gesture buffering to prevent accidental inputs
- **Cooldown System**: 1.2-second cooldown between gestures to ensure intentional input
- **Undo Functionality**: Remove the last input with a simple gesture
- **Performance Metrics**: Real-time latency tracking from first input to result
- **Visual Feedback**: Live video feed with hand landmarks and expression display
- **Error Handling**: Gracefully handles invalid expressions

## 🔧 How It Works

The application captures video from your webcam and processes each frame through the following pipeline:

1. **Frame Capture**: Reads frames from the webcam
2. **Hand Detection**: MediaPipe detects hand landmarks (21 points per hand)
3. **Finger Counting**: Analyzes finger positions to count extended fingers
4. **Gesture Mapping**: Maps finger counts to numbers/operators
5. **Gesture Stabilization**: Uses a buffer to confirm stable gestures
6. **Expression Building**: Constructs mathematical expressions
7. **Calculation**: Evaluates expressions when '=' gesture is shown
8. **Display**: Shows expression, result, and latency on screen

## 📦 Requirements

### System Requirements
- **Operating System**: Windows, macOS, or Linux
- **Webcam**: Built-in or external camera
- **Python**: Version 3.7 or higher

### Python Libraries
- `opencv-python`: For video capture and processing
- `mediapipe`: For hand tracking and landmark detection
- `collections`: For gesture buffering (built-in)
- `time`: For performance tracking (built-in)

## 🚀 Installation

### Step 1: Clone the Repository

```bash
git clone https://github.com/YappityYap/hand-gesture-calculator.git
cd hand-gesture-calculator
```

### Step 2: Create a Virtual Environment (Recommended)

**Windows:**
```bash
python -m venv venv
venv\Scripts\activate
```

**macOS/Linux:**
```bash
python3 -m venv venv
source venv/bin/activate
```

### Step 3: Install Required Libraries

```bash
pip install opencv-python mediapipe
```

**Or install all dependencies at once:**
```bash
pip install opencv-python==4.8.1.78 mediapipe==0.10.8
```

### Step 4: Verify Installation

```bash
python --version  # Should show Python 3.7 or higher
pip list | grep -E "opencv|mediapipe"
```

## 🎮 Usage

### Starting the Application

```bash
python gesture_calculator.py
```

### Using the Calculator

1. **Position yourself**: Sit in front of your webcam with good lighting
2. **Show gestures**: Hold your hand steady in front of the camera
3. **Wait for recognition**: Each gesture requires ~1.2 seconds of stability
4. **Build expressions**: Combine numbers and operators
5. **Calculate**: Show the '=' gesture to see the result
6. **Exit**: Press 'q' on your keyboard to quit

### Example Calculation: 5 + 3 =

1. Show **5 fingers** on your right hand → Input: `5`
2. Show **1 finger** on your left hand → Input: `+`
3. Show **3 fingers** on your right hand → Input: `3`
4. Show **5 fingers** on your left hand → Input: `=`
5. Result: `8`

## 🤚 Gesture Mapping

### Right Hand (Numbers)

| Fingers Extended | Number | Description |
|-----------------|--------|-------------|
| 0 (Fist) | 0 | Zero |
| 1 (Index) | 1 | One |
| 2 (Index + Middle) | 2 | Two |
| 3 (Index + Middle + Ring) | 3 | Three |
| 4 (All except thumb) | 4 | Four |
| 5 (All fingers) | 5 | Five |

### Right Hand with Left Hand Assist (6-9)

| Left Hand | Right Hand | Number | Description |
|-----------|------------|--------|-------------|
| 5 fingers | 1 finger | 6 | Six |
| 5 fingers | 2 fingers | 7 | Seven |
| 5 fingers | 3 fingers | 8 | Eight |
| 5 fingers | 4 fingers | 9 | Nine |

### Left Hand (Operators & Commands)

| Fingers Extended | Symbol | Operation |
|-----------------|--------|-----------|
| 0 (Fist) | U | Undo (remove last input) |
| 1 (Index) | + | Addition |
| 2 (Index + Middle) | - | Subtraction |
| 3 (Index + Middle + Ring) | * | Multiplication |
| 4 (All except thumb) | / | Division |
| 5 (All fingers) | = | Equals (calculate result) |

### Tips for Accurate Gesture Recognition

- **Lighting**: Ensure good, even lighting on your hands
- **Background**: Use a plain background for better detection
- **Distance**: Position your hands 1-2 feet from the camera
- **Steadiness**: Hold gestures steady for at least 1.5 seconds
- **Orientation**: Face your palm towards the camera
- **One at a time**: Show one gesture at a time, wait for recognition

## 🔬 Technical Details

### Architecture

```
gesture_calculator.py
├── Hand Detection (MediaPipe)
│   ├── Landmark Detection (21 points per hand)
│   └── Hand Classification (Left/Right)
├── Finger Counting Algorithm
│   ├── Thumb Detection (X-axis comparison)
│   └── Other Fingers (Y-axis comparison)
├── Gesture Mapping
│   ├── Number Recognition (0-9)
│   └── Operator Recognition (+, -, *, /, =, U)
├── Gesture Stabilization
│   ├── Buffer (deque, maxlen=5)
│   └── Cooldown Timer (1.2 seconds)
└── Expression Evaluation
    ├── Expression Builder
    ├── Python eval() for calculation
    └── Error Handling
```

### Key Components

#### 1. Hand Detection Configuration
```python
hands = mp_hands.Hands(
    max_num_hands=2,              # Detect up to 2 hands
    min_detection_confidence=0.7  # 70% confidence threshold
)
```

#### 2. Finger Counting Logic
- **Thumb**: Compares X-coordinates (different for left/right hands)
- **Other Fingers**: Compares Y-coordinates of tip vs. base knuckle

#### 3. Gesture Stabilization
- **Buffer Size**: 5 consecutive frames
- **Cooldown**: 1.2 seconds between same gestures
- **Purpose**: Prevents accidental/jittery inputs

#### 4. Performance Tracking
- Measures time from first input to result calculation
- Displays latency in seconds with 4 decimal precision

### Display Information

The application window shows:
- **Live video feed** with hand landmarks drawn
- **Expression**: Current mathematical expression being built
- **Result**: Calculated result or error message
- **Latency**: Time taken from first input to result (in seconds)

## 🐛 Troubleshooting

### Camera Access Issues

**Windows:**
1. Go to **Settings** → **Privacy** → **Camera**
2. Enable "Allow apps to access your camera"
3. Enable for Python specifically

**macOS:**
1. Go to **System Preferences** → **Security & Privacy** → **Camera**
2. Check the box next to Terminal or your Python IDE

**Linux:**
1. Ensure your user is in the `video` group:
   ```bash
   sudo usermod -a -G video $USER
   ```
2. Restart your session

### Performance Issues (Slow/Laggy)

**Solution 1: Reduce Camera Resolution**

Add these lines after `cap = cv2.VideoCapture(0)`:
```python
cap.set(cv2.CAP_PROP_FRAME_WIDTH, 640)
cap.set(cv2.CAP_PROP_FRAME_HEIGHT, 480)
```

**Solution 2: Reduce Detection Confidence**
```python
hands = mp_hands.Hands(
    max_num_hands=2,
    min_detection_confidence=0.5  # Lower from 0.7
)
```

**Solution 3: Process Every Nth Frame**
```python
frame_count = 0
while True:
    success, img = cap.read()
    frame_count += 1
    if frame_count % 2 == 0:  # Process every 2nd frame
        continue
    # ... rest of code
```

### Hand Not Detected

**Common Causes:**
- Poor lighting conditions
- Hand too close or too far from camera
- Background too cluttered
- Fingers not clearly separated

**Solutions:**
- Improve lighting (face a window or add a lamp)
- Position hand 1-2 feet from camera
- Use a plain background
- Spread fingers clearly apart

### Gestures Not Recognized

**Issues:**
- Moving hand too quickly
- Not holding gesture steady
- Multiple gestures shown simultaneously

**Solutions:**
- Hold each gesture for 2 full seconds
- Wait for the cooldown period between gestures
- Show one hand gesture at a time
- Keep hand stable and clearly visible

### Error in Calculation

**Causes:**
- Invalid mathematical expression
- Division by zero
- Malformed operator sequence

**Solutions:**
- Use Undo (fist on left hand) to remove incorrect inputs
- Build expressions carefully: number → operator → number
- The app will display "Error" for invalid expressions

### Import Errors

If you get `ModuleNotFoundError`:

```bash
pip uninstall opencv-python opencv-python-headless
pip install opencv-python
pip install mediapipe
```

## ⚡ Performance Optimization

### For Low-End Systems

1. **Reduce hand tracking quality:**
   ```python
   hands = mp_hands.Hands(
       max_num_hands=1,  # Track only 1 hand
       min_detection_confidence=0.5,
       min_tracking_confidence=0.5
   )
   ```

2. **Lower resolution:**
   ```python
   cap.set(cv2.CAP_PROP_FRAME_WIDTH, 480)
   cap.set(cv2.CAP_PROP_FRAME_HEIGHT, 360)
   ```

3. **Skip frames:**
   Process every 2nd or 3rd frame to reduce CPU usage

### For High-End Systems

1. **Increase quality:**
   ```python
   hands = mp_hands.Hands(
       max_num_hands=2,
       min_detection_confidence=0.8,
       min_tracking_confidence=0.8
   )
   ```

2. **Higher resolution:**
   ```python
   cap.set(cv2.CAP_PROP_FRAME_WIDTH, 1280)
   cap.set(cv2.CAP_PROP_FRAME_HEIGHT, 720)
   ```

## 🎓 Credits

This project is inspired by and references:
- [Hand Gesture Calculator using Mediapipe by Anjuk1989](https://github.com/Anjuk1989/Hand-Gesture-Calculator-using-Mediapipe)
- [Tutorial Video](https://www.youtube.com/watch?v=ybPpqNrFYf0)
- [Google MediaPipe](https://google.github.io/mediapipe/) - Hand tracking solution
- [OpenCV](https://opencv.org/) - Computer vision library

## 📄 License

This project is open source and available for educational purposes. Feel free to use, modify, and distribute as needed.

## 🤝 Contributing

Contributions, issues, and feature requests are welcome! Feel free to:
1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📞 Support

If you encounter any issues or have questions:
1. Check the [Troubleshooting](#troubleshooting) section
2. Open an issue on GitHub
3. Review existing issues for solutions

---

**Made with ❤️ using Python, OpenCV, and MediaPipe**
