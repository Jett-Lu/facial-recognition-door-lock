# IntelliGuard Facial Recognition Door Lock

Smart facial recognition based access control system built on a Raspberry Pi 4.
Uses a camera for recognition, an LCD for instructions, LED status feedback, a buzzer for confirmation, Pushbullet notifications, and CSV event logging.

<img width="1536" height="1024" alt="render" src="https://github.com/user-attachments/assets/69e1c797-1c80-43dc-8b5a-31bf34ffbc31" />

## Features
- Facial recognition access control using OpenCV and face-recognition
- LCD prompts and user feedback
- RGB LED status indicators (locked, unlocked, unknown)
- Buzzer confirmation on access granted
- Pushbullet mobile notifications
- CSV event logging (authorized and unauthorized events)

## System Overview

### Architecture
<img width="1518" height="648" alt="image" src="https://github.com/user-attachments/assets/85b35380-2d02-440b-9bbf-a82056a96739" />

<img width="825" height="1173" alt="image" src="https://github.com/user-attachments/assets/286e5bd8-f387-458a-9bbe-72d9d33232b0" />

### Hardware Wiring
<img width="1887" height="864" alt="image" src="https://github.com/user-attachments/assets/ee87752f-ea1c-4df9-a21e-9bc989c5066d" />

## Hardware Used
- Raspberry Pi 4 Model B
- USB camera (Microsoft LifeCam Cinema or equivalent)
- LCD1602 I2C display
- RGB LED (common cathode) + resistors
- Piezo buzzer

## Setup

### 1) Clone repo
Clone this repository onto your Raspberry Pi.

### 2) Python environment
Recommended:
- Python 3.9 to 3.11
- Virtual environment

Install dependencies:
pip install -r requirements.txt

Note: dlib and face-recognition can be sensitive to platform and Python version. If installation fails, build dlib from source or use a compatible wheel for your Pi OS.

### 3) Environment variables
Create a .env file based on the example:

cp .env.example .env

Then set:
- PUSHBULLET_ACCESS_TOKEN=your_token_here

Do not commit .env or any tokens.

### 4) Add authorized users
Add authorized user images under the dataset directory. Each user should have their own folder.

Example:
dataset/
  Alice/
    1.jpg
    2.jpg
  Bob/
    1.jpg
    2.jpg

Use clear, well lit images with multiple angles.

## Running

Start the application on the Raspberry Pi:
python app.py

A local URL will be printed in the console.
Open that URL on another device on the same network to view the live stream and run detection.

## How It Works
High level flow:
1. Encode known user faces from the dataset directory into embeddings
2. Capture frames from the camera
3. Detect faces and compute embeddings for faces in the frame
4. Compare embeddings against known users with a distance threshold
5. If authorized:
   - LCD displays user name and access granted
   - LED flashes green
   - buzzer beeps once (cooldown prevents repeated buzzing)
   - event logged to CSV
   - Pushbullet notification sent (rate limited to reduce spam)
6. If unknown:
   - LCD prompts user to contact admin
   - LED flashes red
   - event logged and notification sent (rate limited)

## Event Logging
All access attempts are written to a CSV file with timestamps for auditing.

Example fields:
- timestamp
- recognized_name or unknown
- event_type (authorized or unauthorized)
- notes

## Security Notes
- Do not store credentials or tokens in the repo
- Use .env and keep it ignored by git
- Rotate tokens if they were ever exposed publicly

## Known Limitations
- Raspberry Pi performance can limit frame rate when streaming and running recognition simultaneously
- Lighting conditions can affect recognition accuracy

## Improvements Planned
- Offload recognition to a stronger device or use an optimized pipeline to improve FPS
- Better UI and admin workflow for managing users
- Additional security hardening and configuration options

## Credits
Jett Lu
Tommy Thai
