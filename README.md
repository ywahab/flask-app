CI/CD with Jenkins:  Deploying a Flask API Application
Prerequisites
Before we dive in, make sure you have the following Software:
    • Jenkins
    • Python 3.x
    • Git
    • Flask
    • Docker and Docker Compose
Set up a Jenkins Server:
    • Download and install Jenkins from the official site.
    • Configure Jenkins by adding necessary plugins like Git and Pipeline.
Server/VM:
    • A Linux server (or a local VM) with access to Jenkins, Python, and Docker.
Step 1: Create a Simple Flask API
Let’s start by creating a basic Flask application.

# app.py
from flask import Flask, jsonify
app = Flask(__name__)@app.route('/')
def home():
    return jsonify({"message": "Welcome to the Flask API!"})if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
    
Save this file as app.py.

Create a Virtual Environment and Install Flask

python3 -m venv venv
source venv/bin/activate
pip install flask

Test the Application
Run the Flask app locally:

python app.py

Access it at http://localhost:5000/.
Step 2: Dockerize the Flask Application
Create a Dockerfile to containerize the Flask application:

# Dockerfile
FROM python:3.9-slim
WORKDIR /appCOPY requirements.txt requirements.txt
RUN pip install -r requirements.txtCOPY . .CMD ["python", "app.py"]

Create a requirements.txt file with the following content:

flask

Build and Run the Docker Container
Build the Docker image:

docker build -t flask-api .

Run the Docker container:

docker run -d -p 5000:5000 flask-api

Access the application at http://localhost:5000/.
Step 3: Set Up a Git Repository
Initialize a Git repository for the Flask application:

git init
git add .
git commit -m "Initial commit"
git remote add origin <your-repo-url>
git push -u origin main

Step 4: Configure Jenkins Pipeline
Create a Jenkins Job
    1. Log in to Jenkins.
    2. Click on New Item.
    3. Select Pipeline and name the job (e.g., “Flask API Deployment”).
    4. Click OK.
Define the Pipeline
In the Pipeline section, choose Pipeline script and paste the following code:

pipeline {
    agent any
stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/ywahab/flask-app.git', branch: 'main'
            }
        }stage('Build Docker Image') {
            steps {
                sh 'docker build -t flask-api .'
            }
        }stage('Run Docker Container') {
            steps {
                sh '''
                docker stop flask-api || true
                docker rm flask-api || true
                docker run -d -p 5000:5000 --name flask-api flask-api
                '''
            }
        }
    }
}

Replace <your-repo-url> with your Git repository URL.
Step 5: Test the Jenkins Pipeline
    1. Save the Jenkins job configuration.
    2. Click Build Now.
    3. Monitor the console output to ensure each stage completes successfully.
Step 6: Verify the Deployment
Once the pipeline finishes, check if the Flask app is running:
    • Access the API at http://<server-ip>:5000/.

