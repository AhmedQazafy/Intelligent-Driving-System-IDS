# Intelligent Driving System (IDS)

> **1st Place** — El Shrouk Academy Graduation Project Competition (July 2024)  
> **2nd Place** — National Engineering Competition (September 2023)

A real-time embedded driver monitoring system built on Raspberry Pi that detects drowsiness and distraction using computer vision, triggers graduated alerts, logs incidents to Firebase, and initiates an emergency GSM call with GPS coordinates on severe drowsiness events.

---

## The Problem

Driver fatigue and inattention are among the leading causes of road accidents globally. Existing solutions are either expensive in-vehicle hardware locked to specific car models, or mobile apps that are intrusive and imprecise. HIMOQ is a self-contained, affordable, embedded system that can be mounted in any vehicle.

---

## How It Works

### Activation
A **PIR motion sensor** detects when a person sits in the driver's seat and activates the system automatically. No manual start required.

### Head Pose Detection
The system uses a trained **scikit-learn pose model** to continuously estimate the driver's head orientation in three axes:
- **Pitch** — head nodding forward (microsleep indicator)
- **Yaw** — head turning left/right (distraction indicator)  
- **Roll** — head tilting sideways (fatigue indicator)

Two computer vision backends were developed and benchmarked:

| Backend | Approach | Performance |
|---|---|---|
| **MediaPipe** | 468-landmark face mesh, real-time | Primary — production model |
| **dlib** | 68-landmark facial geometry | Benchmark / fallback |

### Alert System
Alerts are graduated based on severity and duration of detected inattention:

```
Mild distraction  →  Audio beep warning
Sustained         →  Escalating audio alerts  
Severe drowsiness →  Emergency GSM call + GPS coordinates transmitted
```

### Cloud Logging
All drowsiness and distraction events are logged in real-time to **Firebase Firestore** (project: `driver-live-j61jka`), enabling post-trip analysis and fleet monitoring use cases.

### Emergency Response
On a severe event, the system triggers an **A9G GSM/GPS module** to place an emergency phone call and transmit the vehicle's current GPS coordinates.

---

## System Architecture

```
┌─────────────────────────────────────────────────────┐
│                  Raspberry Pi                        │
│                                                     │
│  ┌──────────┐    ┌─────────────┐    ┌────────────┐  │
│  │  Camera  │───▶│  MediaPipe  │───▶│  sklearn   │  │
│  │  Module  │    │  Face Mesh  │    │ Pose Model │  │
│  └──────────┘    └─────────────┘    └─────┬──────┘  │
│                                           │         │
│  ┌──────────┐                      ┌──────▼──────┐  │
│  │   PIR    │──── Activation ─────▶│   Alert     │  │
│  │  Sensor  │                      │   Engine    │  │
│  └──────────┘                      └──────┬──────┘  │
│                                           │         │
│                              ┌────────────┼──────┐  │
│                              │            │      │  │
│                         ┌────▼───┐  ┌────▼───┐   │  │
│                         │Firebase│  │  A9G   │   │  │
│                         │  Log   │  │GSM/GPS │   │  │
│                         └────────┘  └────────┘   │  │
└──────────────────────────────────────────────────┘
```

---

## Repository Structure

```
HIMOQ/
├── mediapipe model/          # Primary production model
│   └── mediapipe_code.py     # Real-time head pose via MediaPipe
├── dlib(demo) model/         # Benchmark model using dlib
├── pi integration/           # Raspberry Pi deployment scripts
│   └── (PIR, GSM, Firebase integration)
├── data analysis/            # Post-trip drowsiness analytics
│   └── analysis_code.py      # Jupyter-compatible analysis
├── data/                     # Training and test datasets
├── audio/                    # Alert sound files
├── docs/
│   ├── IDS_Book.pdf          # Full graduation project documentation
│   └── IDS_Presentation.pdf  # Project presentation slides
├── .gitignore
└── requirements.txt
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| Hardware | Raspberry Pi, PIR Sensor, A9G GSM/GPS Module, Camera Module |
| Computer Vision | MediaPipe Face Mesh, dlib |
| ML Model | scikit-learn (pitch/yaw/roll pose classification) |
| Cloud | Firebase Firestore (real-time event logging) |
| Language | Python 3 |
| Analysis | Jupyter Notebook, pandas |

---

## Getting Started

### Prerequisites
```bash
pip install mediapipe opencv-python scikit-learn firebase-admin numpy
```

### Run the Main Model (Desktop / Demo)
```bash
cd "mediapipe model"
python mediapipe_code.py
```

### Run on Raspberry Pi
```bash
cd "pi integration"
# Follow setup instructions in docs/IDS_Book.pdf for hardware wiring
python main_pi.py
```

### Data Analysis
```bash
cd "data analysis"
jupyter notebook
# Open analysis_code.ipynb
```

---

## Documentation

Full project documentation including system design, hardware schematics, model training methodology, and test results:

- [Project Book (PDF)](docs/IDS_Book.pdf)
- [Presentation Slides (PDF)](docs/IDS_Presentation.pdf)

---

## Competition Results

| Competition | Place | Date |
|---|---|---|
| El Shrouk Academy Graduation Project Exhibition | 🥇 1st Place | July 2024 |
| National Engineering Student Competition | 🥈 2nd Place | September 2023 |

---

## Acknowledgements
- Special thanks to **[El Shrouk Academy](https://sha.edu.eg/)** for the material and moral support they gave us.
- Special thanks to the creators of dlib and mediapipe for their powerful libraries.
- Gratitude to the open-source community for continuous support and contributions.

## License

This project is open source. See [LICENSE](LICENSE) for details.

## Contributors

We'd like to acknowledge the following individuals who have contributed to this project:

- **[Osama Hegazy](https://github.com/system2001)** 
  
- **[Ahmed Qazafy](https://github.com/AhmedQazafy)** 

Thank you for your valuable contributions!

