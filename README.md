# 🖐️ Hand Gesture Calculator

A real-time calculator that uses hand gestures detected through your webcam to perform mathematical operations. Built with OpenCV and MediaPipe.

## ✨ Features

- **Real-time Hand Detection**: Uses MediaPipe for accurate hand landmark detection
- **Gesture-based Input**: Control calculator with simple hand gestures
- **Basic Operations**: Addition, subtraction, multiplication, and division
- **Undo Functionality**: Remove last entered digit/operator
- **Visual Feedback**: Live camera feed with gesture recognition overlay

## 🎯 Gesture Mapping

### Numbers (Right Hand)
- **0-5 fingers**: Numbers 0-5
- **Left hand (5 fingers) + Right hand (1-4 fingers)**: Numbers 6-9

### Operations (Left Hand)
- **0 fingers**: Undo (U)
- **1 finger**: Addition (+)
- **2 fingers**: Subtraction (-)
- **3 fingers**: Multiplication (*)
- **4 fingers**: Division (/)
- **5 fingers**: Equals (=)

## 🚀 Installation

### Prerequisites
- Python 3.7 or higher
- Webcam
- Windows/Mac/Linux

### Setup

1. **Clone or download this repository**
```bash
git clone https://github.com/yourusername/hand-gesture-calculator.git
cd hand-gesture-calculator
```

2. **Create a virtual environment**
```bash
python -m venv venv
```

3. **Activate virtual environment**
```bash
# Windows
venv\Scripts\activate

# Mac/Linux
source venv/bin/activate
```

4. **Install required packages**
```bash
pip install opencv-python mediapipe
```

## 🎮 Usage

1. **Run the application**
```bash
python gesture_calculator.py
```

2. **Position your hands in front of the camera**
   - Use your right hand for numbers
   - Use your left hand for operations
   - Keep gestures steady for recognition

3. **Perform calculations**
   - Show number gestures to input digits
   - Use operation gestures for math functions
   - Use equals gesture to calculate result

4. **Exit**
   - Press `q` to quit the application

## 📁 Project Structure

```
handgesture/
│
├── gesture_calculator.py    # Main application file
├── README.md               # This file
└── requirements.txt        # Dependencies (optional)
```

## 🛠️ Configuration

### Camera Settings
If the application runs slowly, you can adjust camera resolution in the code:

```python
cap.set(cv2.CAP_PROP_FRAME_WIDTH, 640)
cap.set(cv2.CAP_PROP_FRAME_HEIGHT, 480)
```

### Detection Sensitivity
Adjust gesture detection confidence:

```python
hands = mp_hands.Hands(
    max_num_hands=2, 
    min_detection_confidence=0.7  # Lower for easier detection
)
```

## 🐛 Troubleshooting

### Camera Access Issues
- **Windows**: Settings → Privacy → Camera → Allow apps to access camera
- **Mac**: System Preferences → Security & Privacy → Camera
- **Linux**: Check camera permissions and drivers

### Common Issues
- **Gestures not recognized**: Ensure good lighting and clear hand visibility
- **Lag/slow performance**: Lower camera resolution or close other applications
- **Wrong hand detection**: Make sure hands are clearly separated in camera view


## 📋 Requirements

```txt
opencv-python>=4.5.0
mediapipe>=0.8.0
```

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- [MediaPipe](https://mediapipe.dev/) for hand detection
- [OpenCV](https://opencv.org/) for computer vision tools


---

⭐ **Star this repo if you found it helpful!**
