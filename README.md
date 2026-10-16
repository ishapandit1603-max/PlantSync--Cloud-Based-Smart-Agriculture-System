**PlantSync — Smart Agriculture System**
PlantSync is a cloud-based AI/ML-powered smart agriculture system that combines IoT, AWS Cloud, Machine Learning, and multi-agent AI to monitor farm conditions, predict crop health, detect sensor faults, and automate irrigation.

**Project Objective**
The main objective of PlantSync is to demonstrate how cloud computing, machine learning, IoT, and multi-agent AI can work together to improve polyhouse farm monitoring, identify equipment faults, support crop-health decisions, and reduce unnecessary irrigation.

**Key Features**
- **Real-Time Monitoring**: Tracks soil moisture, temperature, humidity, pH, nutrients, sunlight, and rainfall using virtual ESP32 sensors.
- **AWS Cloud Integration**: Sends sensor data through MQTT to AWS IoT Core, Lambda, DynamoDB, and S3.
- **AI Agent System**: Uses seven specialized agents for telemetry, anomaly detection, self-healing, irrigation control, prediction, agronomy knowledge, and farm assistance.
- **Anomaly Detection**: Identifies abnormal, missing, or stuck sensor readings.
- **Self-Healing**: Detects sensor faults, attempts recovery, and verifies whether the sensor is working again.
- **Crop Prediction**: Uses a Random Forest model to estimate crop health and yield.
- **Smart Irrigation**: Controls simulated pumps, fertiliser dosers, and cooling fans using configurable thresholds.
- **Farm AI Assistant**: Uses a Llama-class model through Groq to answer farm-related questions and support permitted control actions.
- **What-If Lab**: Allows users to change sensor values and observe prediction changes.
- **Interactive Dashboard**: Displays sensor readings, alerts, predictions, agent status, actuator controls, and cloud snapshots.
- 
**System Architecture**
Virtual ESP32 Sensors
        ↓ MQTT
    AWS IoT Core
        ↓
    AWS Lambda
        ↓
    DynamoDB → S3
        ↓
 FastAPI + AI Agents
        ↓
 Random Forest + Llama
        ↓
 React Dashboard
        ↓
 Simulated Farm Controls


**Technology Stack**
- **Programming**: Python, JavaScript
- **Machine Learning**: Scikit-learn, Random Forest
- **Backend**: FastAPI
- **Frontend**: React, Vite, Recharts
- **Cloud**: AWS IoT Core, Lambda, DynamoDB, S3, EC2
- **Communication**: MQTT over TLS
- **LLM**: Llama-class model through Groq
- **Deployment**: Docker and Docker Compose
  
