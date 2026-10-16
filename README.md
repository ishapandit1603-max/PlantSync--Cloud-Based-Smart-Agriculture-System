🌱 PlantSync
Cloud-Based AI/ML-Powered & IoT-Enabled Smart Agriculture System
Monitor smarter. Predict earlier. Irrigate efficiently.
PlantSync is a cloud-based smart agriculture digital twin that combines IoT telemetry, multi-agent AI, machine learning, automated actuator control, and a conversational farm assistant to help monitor and manage agricultural fields.

📌 Table of Contents
- Overview
- Why PlantSync?
- Key Features
- System Architecture
- Technology Stack
- Seven AI Agents
- Machine Learning and Farm Assistant
- Smart Control Logic
- Dashboard Pages
- Project Structure
- AWS Architecture
- Deployment Overview
- Security and Cost Considerations
- Limitations
- Future Scope
- Sustainable Development Goals
🌾 Overview
PlantSync is a cloud-based, AI/ML-powered and IoT-enabled smart agriculture system designed to demonstrate how connected sensors and intelligent software can support data-driven farming.
The current prototype simulates two agricultural fields:
- Field A: Tomato
- Field B: Maize
Virtual ESP32 devices generate telemetry for soil moisture, temperature, humidity, pH, nitrogen, phosphorus, potassium, sunlight, and rainfall. These readings travel through MQTT to AWS IoT Core and are processed by cloud services and a FastAPI multi-agent backend.
A React dashboard displays farm conditions, model predictions, alerts, sensor faults, water-saving estimates, and actuator states.
PlantSync acts as a digital twin-style demonstration, representing simulated field conditions in software and allowing users to explore monitoring, prediction, fault handling, and control workflows.
🎯 Why PlantSync?
Polyhouses and controlled farming environments require regular monitoring because changes in temperature, humidity, soil moisture, and nutrients can affect crop growth.
PlantSync aims to help by:
- Monitoring multiple fields from one dashboard.
- Identifying unusual or potentially faulty sensor readings.
- Exploring crop-health and yield predictions.
- Demonstrating irrigation and other actuator controls.
- Comparing simulated smart irrigation against a fixed-timer baseline.
- Making farm information easier to understand through a conversational assistant.
✨ Key Features
- Live telemetry dashboard for two simulated fields.
- MQTT-based IoT communication through AWS IoT Core.
- Seven specialised AI agents coordinated by an orchestrator.
- Anomaly detection for missing, out-of-range, sudden-jump, and stuck readings.
- Self-healing workflow following Detect → Diagnose → Act → Verify.
- Random Forest regression to estimate crop health and expected yield.
- What-If Lab to explore how changing sensor conditions affects predictions.
- Smart actuator controls for an irrigation pump, fertiliser doser, and cooling fan.
- Farm Assistant using a Groq-hosted Llama-class model and live farm context.
- Fault Lab for injecting simulated sensor faults and observing responses.
- Cloud snapshots stored in DynamoDB and Amazon S3.
- Water-saved estimates compared against a configured fixed-timer baseline.
- Public dashboard URL when deployed with suitable EC2 and network configuration.
🏗️ System Architecture
Virtual ESP32 Sensors
        |
        | MQTT over TLS
        v
   AWS IoT Core
        |
        v
 AWS IoT Rules Engine
        |
        v
    AWS Lambda
 (validate and flag)
        |
        v
  Amazon DynamoDB  <------> FastAPI Multi-Agent Backend
        |                         |
        |                         +-- Telemetry Agent
        |                         +-- Anomaly Detection Agent
        |                         +-- Self-Healing Agent
        |                         +-- Irrigation / Control Agent
        |                         +-- Prediction Agent (Random Forest)
        |                         +-- Knowledge Agent
        |                         +-- Farm Copilot (Llama via Groq)
        |
        +------ Amazon S3 Snapshots
        |
        v
 React + Recharts Dashboard
        |
        | Supported control requests
        v
 Backend / MQTT Command Pathway
        |
        v
 Simulated or Configured Actuators
 (Pump, Fertiliser Doser, Cooling Fan)


How the Data Flows
1. Sense: Virtual ESP32 devices generate readings for the simulated fields.
2. Transmit: Devices publish telemetry using MQTT over TLS to AWS IoT Core.
3. Route: An AWS IoT rule forwards matching telemetry messages to AWS Lambda.
4. Validate and store: Lambda checks readings, flags suspicious values, and writes processed records to DynamoDB.
5. Analyse: FastAPI coordinates the agents, ML predictions, fault-handling workflow, and supported control actions.
6. Persist: Farm-state snapshots are stored in DynamoDB and Amazon S3 at the configured interval or on demand.
7. Visualise: React and Recharts display readings, alerts, predictions, and actuator states.
8. Control: Supported commands travel through the configured backend/MQTT pathway, and subsequent telemetry helps monitor the resulting state.
🧰 Technology Stack
Layer	Technologies	Purpose
Simulated devices	Virtual ESP32, Python, CSV	Generate telemetry and test sensor faults
Messaging	MQTT, AWS IoT Core	Device connectivity and message routing
Event processing	AWS Lambda	Validate and process incoming telemetry
Database	Amazon DynamoDB	Store readings, events, and state records
Object storage	Amazon S3	Store farm-state JSON snapshots
Backend	Python, FastAPI	APIs and agent orchestration
Containers	Docker, Docker Compose	Package and run application services
Machine learning	scikit-learn, Random Forest	Crop-health and yield estimation
Conversational AI	Llama-class model via Groq	Farm questions and supported tool calling
Knowledge retrieval	Agronomy notes and retrieval logic	Provide relevant agricultural context
Frontend	React, Vite, Recharts	Interactive dashboard and visual analytics
Monitoring	Amazon CloudWatch	Logs and metrics
Access control	AWS IAM	Manage permissions
Hosting	Amazon EC2	Host the application stack
🤖 The Seven AI Agents
The agents have distinct responsibilities. Not every agent uses a large language model.
Agent	Responsibility
1. Telemetry Agent	Validates, timestamps, and forwards sensor packets and coordinates snapshots.
2. Anomaly Detection Agent	Detects missing, out-of-range, sudden-jump, or stuck sensor readings.
3. Self-Healing Agent	Diagnoses sensor problems, attempts configured recovery, and verifies recovery.
4. Irrigation / Control Agent	Applies configured rules to the pump, fertiliser doser, and cooling fan.
5. Prediction Agent	Uses Random Forest to estimate crop health and expected yield.
6. Knowledge Agent	Retrieves relevant agricultural guidance from agronomy notes.
7. Farm Copilot	Answers farm questions using live state and retrieved knowledge, and can invoke supported actuator tools.
An orchestrator coordinates the agent workflow.
Self-Healing Workflow
Detect → Diagnose → Act → Verify
The prototype can flag suspicious readings and attempt configured recovery actions, such as using a rolling median or rebooting a stuck simulated node. It checks for four clean readings before treating a sensor as recovered.
This demonstrates sensor-data recovery; it does not mean the crop itself has physically recovered.
🧠 Machine Learning and Farm Assistant
Random Forest Prediction
The current Random Forest regressor is configured with:
- Inputs: Nine sensor-related features.
- Outputs: Estimated crop-health percentage and expected yield in tonnes per hectare.
- Model configuration: 60 trees and maximum depth of 12.
- Training data: 4,000 synthetic agronomy samples.
The What-If Lab lets users change conditions such as moisture, temperature, nitrogen, or pH and compare the resulting model estimates.
Important: The model is a prototype trained on synthetic data. Its predictions have not yet been validated against real farm outcomes.

Farm Assistant
The Farm Copilot combines current farm state, retrieved agronomy knowledge, and a Groq-hosted Llama-class model.
It can:
- Summarise the status of both fields.
- Explain alerts and sensor faults in natural language.
- Answer questions using available farm context.
- Call supported actuator functions when a valid command is requested.
If the external language-model service is unavailable, local command logic and a fallback status summary may still be available, depending on configuration.
🚿 Smart Control Logic
The demonstration uses configurable thresholds:
Actuator	Example rule
Irrigation pump	Turn on below 35% soil moisture when no rain is detected; turn off at 55%.
Cooling fan	Turn on above 34°C; turn off below 30°C.
Fertiliser doser	Turn on below 45 mg/kg nitrogen; turn off at 65 mg/kg.
Manual actions pause automatic control for the configured HOLD_SECONDS period. The prototype also includes Start/Stop controls, a short pump run, Emergency Stop, and Resume Auto.
These values are demonstration settings, not universal recommendations for every crop or soil. Real deployments require crop-specific calibration, suitable hardware safeguards, and validation.
📊 Dashboard Pages
The React dashboard includes seven main pages:
1. Dashboard — field overview, moisture, nutrients, water-saved estimate, and agent activity.
2. Sensor Analytics — sensor trends for moisture, temperature, humidity, pH, sunlight, and nitrogen.
3. Field Map — visual crop-health status for the simulated fields.
4. AI Agents — agent activity and the Fault Lab.
5. Predictions & What-If — Random Forest estimates and scenario comparisons.
6. Actuators & Alerts — pump, fan, doser, alerts, and manual controls.
7. Cloud Snapshots — stored farm-state snapshots.
The exact data and controls shown depend on the running configuration and availability of connected services.
📁 Project Structure
plantsync/
├── backend/
│   ├── app/
│   │   ├── engine.py          # Orchestrator and core agents
│   │   ├── ml.py              # Random Forest prediction
│   │   ├── knowledge.py       # Agronomy knowledge retrieval
│   │   └── copilot.py         # Groq Llama Farm Copilot
│   ├── esp32_sim.py           # Virtual ESP32 fleet
│   └── data/
│       ├── farm_data.csv      # Sensor data with injected faults
│       └── generate_csv.py    # Generate or replace sample data
├── frontend/                  # React + Vite + Recharts dashboard
├── deploy/
│   ├── setup_aws.sh           # AWS resource setup helper
│   ├── ec2_user_data.sh       # EC2 initial setup
│   ├── iot_policy.json        # Example IoT policy
│   └── lambda/
│       └── handler.py         # Lambda ingestion handler
├── Dockerfile
├── docker-compose.yml
├── .env.example
└── README.md


The exact files may vary slightly by project version.
☁️ AWS Architecture
AWS Service	Role in PlantSync
AWS IoT Core	Secure IoT connectivity and MQTT message routing
AWS IoT Rules Engine	Routes telemetry to downstream processing
AWS Lambda	Validates incoming readings and writes records
Amazon DynamoDB	Stores structured telemetry, events, and snapshot records
Amazon S3	Stores complete farm-state snapshots
Amazon EC2	Hosts the FastAPI backend, agents, ML application, and containers
Amazon CloudWatch	Provides logs and metrics for troubleshooting
AWS IAM	Controls access for AWS services and resources
The deployment guide describes the Sydney region (ap-southeast-2). If your actual AWS resources are in another region, configure the application consistently with that region.
🚀 Deployment Overview
The repository includes deployment helpers for setting up AWS resources and launching the application.
Prerequisites
- An AWS account with permission to create the required services.
- A Groq API key for conversational AI features.
- Git and GitHub (optional, but useful for source control).
- Visual Studio Code and Remote - SSH for server-side development.
- Docker and Docker Compose on the host, or the supplied EC2 setup script.
- An AWS billing budget or alert before creating resources.
High-Level Deployment Steps
1. Prepare the repository: Review the code, .env.example, deployment scripts, and required permissions.
2. Configure AWS resources: Set up IoT Core, the IoT rule, Lambda, DynamoDB, S3, and the EC2 instance role.
3. Launch EC2: Choose a supported instance type and configure the security group and instance profile.
4. Configure the application: Add the required IoT certificates and environment variables on the server.
5. Build and start containers:
   docker compose up -d --build
6. Inspect logs:
   docker compose logs -f
7. Verify telemetry: Confirm the simulator connects, MQTT messages arrive, and records appear in DynamoDB.
8. Open the dashboard: Visit the configured public URL once the service and network settings are ready.
Never commit .env, API keys, private keys, or device certificates to a public repository. Use the exact environment-variable names and steps required by your project version. The deployment guide and current AWS console settings should be treated as the source of truth for the live environment.
🔐 Security and Cost Considerations
- Use TLS and device certificates for IoT connections.
- Restrict IoT policies to the required topics and actions.
- Apply least-privilege IAM permissions.
- Keep API keys, private keys, and certificates out of source control.
- Restrict SSH access to trusted IP addresses and expose only required ports.
- Enable appropriate logging, budgets, and billing alerts.
- Run the simulator only when needed for demonstrations; frequent simulated messages can increase usage.
- Stop or remove unused resources when finished. Stopping EC2 alone does not delete every AWS resource or guarantee that all charges stop.
- Review current AWS Free Tier or credit terms and regional prices; eligibility and costs vary by account and usage.
A free-tier-eligible EC2 instance does not mean the entire multi-service architecture is guaranteed to be free.
⚠️ Limitations
- Simulated sensors: Physical sensors have not yet been validated on a real farm.
- Synthetic training data: Model performance needs validation using real agricultural data.
- Fixed thresholds: Control settings may not suit every crop, soil, or growth stage.
- Cloud dependency: Network or cloud outages can affect dependent features.
- LLM limitations: The assistant may misunderstand a request or provide an incorrect answer.
- Limited crop coverage: The demonstration focuses on tomato and maize.
- Fault recovery: Not every physical sensor fault can be corrected automatically.
- Scale not yet proven: Multi-farm performance and operating cost need load testing.
- Simulated water savings: Water-saved figures depend on the configured baseline and need field measurements.
🔭 Future Scope
- Integrate physical ESP32 devices and calibrated farm sensors.
- Validate crop-health, yield, and water-use estimates with real field data.
- Add weather-forecast-aware and crop-specific irrigation strategies.
- Improve offline or edge-based operation during connectivity outages.
- Add leaf-image disease detection and regional-language voice support.
- Strengthen authentication, secret management, certificate rotation, backups, and recovery procedures.
- Conduct load tests and optimise costs before supporting more fields and users.
🌍 Sustainable Development Goals
SDG 2 — Zero Hunger
PlantSync supports better crop monitoring, early crop-stress awareness, and data-informed decisions that may help protect yields and support resilient food production.
SDG 3 — Good Health and Well-being
More targeted fertiliser use may reduce unnecessary chemical exposure and nutrient runoff, supporting healthier people and environments.
SDG 15 — Life on Land
More efficient irrigation and targeted nutrient management may help conserve water, protect soil quality, and support sustainable land management.
These are potential contributions, not measured impact claims; real-world benefits require field validation.
