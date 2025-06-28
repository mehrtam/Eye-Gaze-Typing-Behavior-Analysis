# 👁️⌨️ Eye Gaze & Typing Behavior Analysis

This project analyzes **where a user is looking (screen vs. keyboard)** while typing on a physical keyboard using webcam-based video recordings and face landmark tracking.

It answers questions like:
- *When does the user look at the keyboard vs. the screen?*
- *Which characters are hardest to type without looking?*
- *How much time do they spend looking at the keyboard?*

---

## 🚀 Features

✅ Extract frames from webcam video  
✅ Detect 3D face landmarks (MediaPipe FaceMesh)  
✅ Estimate head pose (pitch, yaw, roll)  
✅ Compute eye movement offsets  
✅ Combine head + eye to get **final gaze direction**  
✅ Align gaze data with **typing events**  
✅ Label each typed character with **where user was looking**  
✅ Analyze:
- Characters typed while looking at keyboard vs. screen
- Letters hardest to find without looking
- Total time spent looking at keyboard vs. screen
✅ Visualize results with charts and images

---


## 🧭 Analysis Steps

### 1️⃣ Frame Extraction
- Split recorded webcam video into PNG frames.
- Example: OpenCV’s `VideoCapture.read()` loop.

---

### 2️⃣ Face Landmark Detection
- Used **MediaPipe FaceMesh** on each frame.
- Extracted (x, y, z) coordinates for 468 landmarks.
- Saved to CSV.

---

### 3️⃣ Head Pose Estimation
- Selected 6 key landmarks (nose, eyes, chin, mouth corners).
- Solved **PnP** to get:
  - Pitch (up/down)
  - Yaw (left/right)
  - Roll (tilt)

- Saved head pose angles per frame.

---

### 4️⃣ Eye Offset Calculation
- Extracted landmarks for left/right eyes.
- Computed **average eye offset** from center.
- Eye offset is proportional to eye-in-head rotation.

---

### 5️⃣ Combined Gaze Estimation
- Combined head yaw and eye offset:

final_gaze_yaw = -(head_yaw - eye_offset * scale)


✅ Corrected sign so **positive = user right, negative = user left**.  

- Categorized **final gaze** as:
  - `left`
  - `center` (user looking at **keyboard**)
  - `right` (user looking at **screen**)

✅ Saved as `combined_category` in CSV.

---

### 6️⃣ Typing Data Alignment
- Merged typing events (timestamp, character) with gaze data.
- Matched typing timestamps to nearest video frame time.
- Result: each typed character is labeled with **where user was looking**.

---

### 7️⃣ Visualization & QA
- Sample frames overlaid with **arrow indicating gaze direction**.
- Verified arrow direction matches **combined_category**.

---

### 8️⃣ Character-Level Analysis

#### ✅ Frequency by Gaze
- Counted how often each character was typed:
  - While looking at **keyboard** (center)
  - While looking at **screen** (right)

- Visualized with side-by-side bar charts.

---

#### ✅ Hardest Characters (Average Looking Time)
- Grouped contiguous **center** periods (looking at keyboard).  
- Measured duration of each period.  
- Distributed duration equally across typed characters in block.  

- For each character:
Avg_Looking_Time_Per_Event = Total_Center_Looking_Time / Count

markdown
Copy
Edit

- Sorted to find **characters needing the most keyboard-looking time**.  
- Example hardest letters:
  - `g`, `j`, `-`, `f`, `K`, ...

✅ These are **hardest to type blind**.

---

### 9️⃣ Overall Keyboard vs. Screen Time
- Summed **total time** user spent:
  - Looking at **keyboard** (center periods)
  - Looking at **screen** (right periods)

- Visualized with **pie chart** showing proportion of session:

Keyboard-looking time: ~145s
Screen-looking time: ~39s

yaml
Copy
Edit

---

## 📊 Example Outputs
- ✅ CSV files with per-character analysis
- ✅ Bar charts comparing typing in center vs right
- ✅ Hardest letters ranked by looking time
- ✅ Pie chart of total keyboard vs screen time
- ✅ Frame images with gaze arrows
