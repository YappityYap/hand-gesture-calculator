https://github.com/Anjuk1989/Hand-Gesture-Calculator-using-Mediapipe/blob/main/Gesture_Calculator.ipynb

https://www.youtube.com/watch?v=ybPpqNrFYf0

1)create
foldername:handgesture
fileName:gesture_calculator.py

2)version
python --version

3)Create a Virtual Environment
python -m venv venv

4)Install Required Libraries
pip install opencv-python mediapipe

5)code
import cv2
import mediapipe as mp
from collections import deque
import time
mp_hands = mp.solutions.hands
mp_draw = mp.solutions.drawing_utils
hands = mp_hands.Hands(max_num_hands=2, min_detection_confidence=0.7)
expression = ""
current_number = ""
result = ""
gesture_buffer = deque(maxlen=5)
last_gesture = None
last_undo_triggered = False
awaiting_new_expression = False
gesture_cooldown = 1.2  # seconds
gesture_timestamp = {}  # maps gestures to last time seen
def count_fingers(landmarks, label):
    fingers = []
    if label == "Right":
        fingers.append(1 if landmarks[4].x < landmarks[3].x else 0)
    else:
        fingers.append(1 if landmarks[4].x > landmarks[3].x else 0)
    for tip_id in [8, 12, 16, 20]:
        fingers.append(1 if landmarks[tip_id].y < landmarks[tip_id - 2].y else 0)
    return sum(fingers)
def map_gesture(right_fingers, left_fingers):
    if right_fingers is not None:
        if left_fingers == 5 and 1 <= right_fingers <= 4:
            return str(right_fingers + 5)  # 6–9
        elif 0 <= right_fingers <= 5:
            return str(right_fingers)     # 0–5

    if left_fingers is not None:
        return {
            0: 'U',
            1: '+',
            2: '-',
            3: '*',
            4: '/',
            5: '='
        }.get(left_fingers, None)

    return None
cap = cv2.VideoCapture(0)
while True:
    success, img = cap.read()
    if not success:
        continue

    img = cv2.flip(img, 1)
    img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
    results = hands.process(img_rgb)

    right_fingers = None
    left_fingers = None

    if results.multi_hand_landmarks and results.multi_handedness:
        for hand_landmarks, handedness in zip(results.multi_hand_landmarks, results.multi_handedness):
            label = handedness.classification[0].label
            mp_draw.draw_landmarks(img, hand_landmarks, mp_hands.HAND_CONNECTIONS)
            finger_count = count_fingers(hand_landmarks.landmark, label)
            if label == "Right":
                right_fingers = finger_count
            elif label == "Left":
                left_fingers = finger_count

    detected_gesture = map_gesture(right_fingers, left_fingers)

    if detected_gesture:
        gesture_buffer.append(detected_gesture)

        if len(gesture_buffer) == gesture_buffer.maxlen and all(g == gesture_buffer[0] for g in gesture_buffer):
            stable_gesture = gesture_buffer[0]
            
            now = time.time()
            last_time = gesture_timestamp.get(stable_gesture, 0)

            if now - last_time > gesture_cooldown:
                gesture_timestamp[stable_gesture] = now

                # 🔠 Gesture Handling
                if stable_gesture.isdigit():
                        if result:
                            result = "0"  # Set result to zero on new input
                        expression += stable_gesture
                        last_operator_gesture = None
                        gesture_buffer.clear()
                elif stable_gesture in ['+', '-', '*', '/']:
                        if result:
                            result = "0"  # Set result to zero on new input
                        if current_number:
                            expression += current_number
                            current_number = ""
                        expression += stable_gesture
                        last_operator_gesture = stable_gesture
                        gesture_buffer.clear()
                elif stable_gesture == '=':
                    try:
                        result = str(eval(expression))
                    except:
                        result = "Error"
                    expression = ""
                elif stable_gesture == 'U':  # Undo
                    expression = expression[:-1]

                gesture_buffer.clear()


                last_gesture = stable_gesture
                gesture_buffer.clear()

    if detected_gesture != 'U':
        last_undo_triggered = False

    display_expr = expression + current_number

    cv2.putText(img, f'Expression: {display_expr}', (10, 60),
                cv2.FONT_HERSHEY_SIMPLEX, 1.1, (255, 255, 255), 2)
    cv2.putText(img, f'Result: {result}', (10, 110),
                cv2.FONT_HERSHEY_SIMPLEX, 1.3, (255, 0, 0), 3)

    cv2.imshow("Gesture Calculator", img)
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()

6)Press q to quit.
7)Troubleshooting
If you get camera access denied, check:

On Windows: Privacy Settings → Camera → Allow apps to use camera.

On Mac/Linux: grant camera permission.

If it’s slow → reduce resolution:
import cv2
import mediapipe as mp
from collections import deque
import time

mp_hands = mp.solutions.hands
mp_draw = mp.solutions.drawing_utils
hands = mp_hands.Hands(max_num_hands=2, min_detection_confidence=0.7)

expression = ""
current_number = ""
result = ""
gesture_buffer = deque(maxlen=5)
last_gesture = None
last_undo_triggered = False
awaiting_new_expression = False
gesture_cooldown = 1.2  # seconds
gesture_timestamp = {}  # maps gestures to last time seen


def count_fingers(landmarks, label):
    fingers = []
    if label == "Right":
        fingers.append(1 if landmarks[4].x < landmarks[3].x else 0)
    else:
        fingers.append(1 if landmarks[4].x > landmarks[3].x else 0)

    for tip_id in [8, 12, 16, 20]:
        fingers.append(1 if landmarks[tip_id].y < landmarks[tip_id - 2].y else 0)
    return sum(fingers)


def map_gesture(right_fingers, left_fingers):
    if right_fingers is not None:
        if left_fingers == 5 and 1 <= right_fingers <= 4:
            return str(right_fingers + 5)  # 6–9
        elif 0 <= right_fingers <= 5:
            return str(right_fingers)     # 0–5

    if left_fingers is not None:
        return {
            0: 'U',
            1: '+',
            2: '-',
            3: '*',
            4: '/',
            5: '='
        }.get(left_fingers, None)

    return None


cap = cv2.VideoCapture(0)

# ✅ Set camera resolution
cap.set(cv2.CAP_PROP_FRAME_WIDTH, 640)
cap.set(cv2.CAP_PROP_FRAME_HEIGHT, 480)

while True:
    success, img = cap.read()
    if not success:
        continue

    img = cv2.flip(img, 1)
    img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
    results = hands.process(img_rgb)

    right_fingers = None
    left_fingers = None

    if results.multi_hand_landmarks and results.multi_handedness:
        for hand_landmarks, handedness in zip(results.multi_hand_landmarks, results.multi_handedness):
            label = handedness.classification[0].label
            mp_draw.draw_landmarks(img, hand_landmarks, mp_hands.HAND_CONNECTIONS)
            finger_count = count_fingers(hand_landmarks.landmark, label)
            if label == "Right":
                right_fingers = finger_count
            elif label == "Left":
                left_fingers = finger_count

    detected_gesture = map_gesture(right_fingers, left_fingers)

    if detected_gesture:
        gesture_buffer.append(detected_gesture)

        if len(gesture_buffer) == gesture_buffer.maxlen and all(g == gesture_buffer[0] for g in gesture_buffer):
            stable_gesture = gesture_buffer[0]

            now = time.time()
            last_time = gesture_timestamp.get(stable_gesture, 0)

            if now - last_time > gesture_cooldown:
                gesture_timestamp[stable_gesture] = now

                # 🔠 Gesture Handling
                if stable_gesture.isdigit():
                    if result:
                        result = "0"  # Set result to zero on new input
                    expression += stable_gesture
                    last_operator_gesture = None
                    gesture_buffer.clear()
                elif stable_gesture in ['+', '-', '*', '/']:
                    if result:
                        result = "0"  # Set result to zero on new input
                    if current_number:
                        expression += current_number
                        current_number = ""
                    expression += stable_gesture
                    last_operator_gesture = stable_gesture
                    gesture_buffer.clear()
                elif stable_gesture == '=':
                    try:
                        result = str(eval(expression))
                    except:
                        result = "Error"
                    expression = ""
                elif stable_gesture == 'U':  # Undo
                    expression = expression[:-1]

                gesture_buffer.clear()
                last_gesture = stable_gesture
                gesture_buffer.clear()

    if detected_gesture != 'U':
        last_undo_triggered = False

    display_expr = expression + current_number

    cv2.putText(img, f'Expression: {display_expr}', (10, 60),
                cv2.FONT_HERSHEY_SIMPLEX, 1.1, (255, 255, 255), 2)
    cv2.putText(img, f'Result: {result}', (10, 110),
                cv2.FONT_HERSHEY_SIMPLEX, 1.3, (255, 0, 0), 3)

    cv2.imshow("Gesture Calculator", img)
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()

