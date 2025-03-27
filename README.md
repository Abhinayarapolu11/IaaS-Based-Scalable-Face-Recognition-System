# IaaS-Based Scalable Face Recognition System

This project focuses on building a scalable Infrastructure-as-a-Service (IaaS) solution on AWS to support real-time facial recognition. It was completed as part of Arizona State University's CSE 546 Cloud Computing course.

## 🔧 Infrastructure Design

The project demonstrates a custom-designed IaaS solution using core AWS services:

- **EC2** – for compute (web and app tiers)
- **S3** – for persistent image and result storage
- **SQS** – for message queuing between system components

The system architecture includes:

- A single **web-tier EC2 instance** for request routing and system control.
- A **pool of application-tier EC2 instances** for model inference, launched dynamically.
- A **custom-built autoscaling controller** (no AWS Auto Scaling used) to manage instance lifecycle.

## 💡 Key Components

### Web Tier (`server.py`)
- Handles incoming HTTP POST requests.
- Stores images in an input S3 bucket.
- Pushes request metadata to an SQS request queue.
- Waits for results from the SQS response queue and returns them to the client.

### App Tier (`backend.py`)
- Runs on EC2 instances created from a custom AMI.
- Each instance processes one message: fetches image from S3, performs inference with a PyTorch model, and uploads result to the output bucket.
- Sends classification result back via SQS.

### Autoscaling Controller (`controller.py`)
- Monitors the request queue size in real time.
- Dynamically launches or terminates app-tier EC2 instances.
- Ensures the app tier scales from 0 to 15 instances based on demand.
- Ensures rapid scale-in (< 5 seconds) after workload completion.

## 🚀 IaaS Implementation Highlights

- Created and managed EC2 AMIs for repeatable, fast app-tier instance launches.
- Handled all instance lifecycle operations programmatically using `boto3`.
- Optimized latency and costs by using stopped-state initialization and real-time scale-in.

## ✅ Testing

- Successfully passed CSE546 autograder tests:
  - 100% classification accuracy
  - Average latency < 1s for 100 concurrent requests
  - Fully dynamic scaling within specified limits (0–15 instances)

## 🔗 Tools & Tech

- AWS EC2, S3, SQS
- Python (boto3, torch, http.server)
- PyTorch
- Shell scripting, Linux

