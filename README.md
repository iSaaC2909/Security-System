# Security Camera System with Person Detection

## Overview

This project is a comprehensive security camera system that detects human motion, records video clips when a person is detected, stores them in cloud storage, and sends notifications. The system consists of:

1. A Python backend with Flask API
2. Computer vision-based person detection
3. Google Cloud Storage integration
4. Twilio SMS notifications
5. React frontend for system control and video playback

## System Architecture

```
Frontend (React) ↔ Flask API ↔ Camera System ↔ Cloud Storage
                        ↓
                  Twilio Notifications
```

## Features

- Real-time person detection using MobileNet SSD
- Automatic video recording when person is detected
- Cloud storage of video clips with public URLs
- SMS notifications with detection timestamps
- Web interface for system control and video playback
- Date-based video log retrieval
- Multi-threaded processing for smooth operation

## Installation

### Prerequisites

1. Python 3.7+
2. Node.js (for frontend)
3. Google Cloud account with Storage bucket
4. Twilio account
5. Webcam or video source

### Backend Setup

1. Clone the repository:
   ```bash
   git clone <repository-url>
   cd security-camera-system
   ```

2. Install Python dependencies:
   ```bash
   pip install opencv-python numpy twilio python-dotenv google-cloud-storage ffmpeg-python flask flask-cors requests
   ```

3. Set up environment variables:
   - Create a `.env` file in the root directory with:
     ```
     TWILIO_ACCOUNT_SID=your_account_sid
     TWILIO_AUTH_TOKEN=your_auth_token
     TWILIO_SEND_NUMBER=+1234567890
     TWILIO_RECEIVE_NUMBER=+0987654321
     ```

4. Set up Google Cloud credentials:
   - Download your service account JSON file and save as `credentials.json`
   - Update `BUCKET_NAME` in `storage.py` with your bucket name

5. Download the deep learning model:
   - Place `config.txt` and `mobilenet_iter_73000.caffemodel` in a `models` directory

### Frontend Setup

1. Navigate to the frontend directory:
   ```bash
   cd frontend
   ```

2. Install dependencies:
   ```bash
   npm install
   npm install react-player
   ```

3. Start the development server:
   ```bash
   npm start
   ```

## Usage

### Running the System

1. Start the Flask backend:
   ```bash
   python main.py
   ```

2. Start the React frontend:
   ```bash
   cd frontend
   npm start
   ```

3. Access the web interface at `http://localhost:3000`

### API Endpoints

- `POST /arm`: Arm the camera system
- `POST /disarm`: Disarm the camera system
- `GET /get-armed`: Check if system is armed
- `GET /get-logs?startDate=YYYY-MM-DD&endDate=YYYY-MM-DD`: Retrieve video logs
- `POST /motion_detected`: Internal endpoint for motion notifications

### System Operation

1. Arm the system via the web interface or API
2. The system will monitor the video feed for persons
3. When detected:
   - Recording starts
   - Continues while person is in frame
   - Stops 50 frames after person leaves
   - Video is uploaded to cloud storage
   - SMS notification is sent
4. View recordings via the web interface

## Configuration

### Camera Settings

- Detection threshold: 0.5 confidence (adjust in `camera.py`)
- Non-detection frames to stop: 50 (adjust in `camera.py`)
- Video resolution: Scaled to 720p (adjust in `storage.py`)

### Storage Settings

- Bucket name: Set in `storage.py`
- File retention: Manual (implement automatic cleanup if needed)

### Notification Settings

- SMS template: Adjust in `notifications.py`
- Recipient number: Set in `.env`

## Technical Details

### Person Detection

- Uses MobileNet SSD model with Caffe framework
- Processes frames at 20 FPS
- Only detects "person" class (ID 15 in COCO dataset)

### Video Processing

1. Raw recording at native camera resolution
2. FFmpeg scales to 720p before upload
3. MP4 format with H.264 codec

### Multi-threading

- Camera feed processing runs in main thread
- Video upload and notification in separate threads
- Prevents frame drops during I/O operations

## Troubleshooting

### Common Issues

1. **Camera not detected**:
   - Check camera index in `cv.VideoCapture(0)`
   - Ensure no other application is using the camera

2. **Model loading errors**:
   - Verify model files are in `models/` directory
   - Check file paths in `camera.py`

3. **Storage upload failures**:
   - Verify `credentials.json` is valid
   - Check bucket name and permissions

4. **SMS not sending**:
   - Verify Twilio credentials in `.env`
   - Check account balance with Twilio

### Logging

Add logging to debug issues:
```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## Security Considerations

1. **Environment variables**:
   - Never commit `.env` or `credentials.json` to version control
   - Use `.gitignore` to exclude sensitive files

2. **API security**:
   - Add authentication for API endpoints
   - Consider HTTPS for production

3. **Video privacy**:
   - Review bucket permissions
   - Consider encryption for sensitive footage

## Scaling and Production

For production deployment:

1. **Containerization**:
   - Dockerize the application
   - Use Docker Compose for services

2. **Database**:
   - Add PostgreSQL/MySQL for video metadata
   - Implement proper indexing for queries

3. **Load balancing**:
   - Use Gunicorn with Nginx for Flask
   - Consider Kubernetes for large deployments

4. **Monitoring**:
   - Implement health checks
   - Set up logging and alerts

## Future Enhancements

1. **Multiple cameras**:
   - Support for RTSP streams
   - Camera switching in UI

2. **Advanced detection**:
   - Face recognition
   - Object classification

3. **Cloud functions**:
   - Automatic video analysis
   - Suspicious activity alerts

4. **Mobile app**:
   - Push notifications
   - Live view

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- OpenCV for computer vision capabilities
- Google Cloud for storage infrastructure
- Twilio for notification services
- React community for frontend tools
