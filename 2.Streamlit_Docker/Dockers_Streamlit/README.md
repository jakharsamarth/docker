🐳📈 Deploying a Streamlit Spiral App Using Docker (Updated)
This guide will walk you through deploying your custom Streamlit Spiral Visualization App inside a Docker container.
By the end, your app will be running in a containerized environment, accessible via your browser. 🚀

Prerequisites 📋
Make sure you have the following installed:

Docker (for containerization)

Git (to manage source code if needed)

Python (to initially test the app if required)

📚 Official References
Docker Docs

Streamlit Docs

Git Docs

Installation 🛠️
Step 1: Install Streamlit Locally (Optional)
If you want to test the app outside Docker first:

bash
Copy
Edit
pip install streamlit pandas altair
Step 2: Install Docker
Install Docker Engine (for Ubuntu):

bash
Copy
Edit
sudo apt update
sudo apt install docker.io
Start Docker and enable it to auto-start:

bash
Copy
Edit
sudo systemctl start docker
sudo systemctl enable docker
Verify Docker:

bash
Copy
Edit
docker --version
✅ Example Output: Docker version 24.0.2, build cb74dfc

🌀 Create Your Streamlit Spiral Application
Inside your project folder (e.g., ~/container-experiments/ex1/), create a file:

app.py
python
Copy
Edit
import streamlit as st
import pandas as pd
import numpy as np
import altair as alt
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'])

def generate_spiral(num_points, num_turns):
    data = []
    for i in range(num_points):
        angle = 2 * np.pi * num_turns * (i / num_points)
        radius = i / num_points
        x = radius * np.cos(angle)
        y = radius * np.sin(angle)
        data.append(Point(x, y))
    return data

st.title("Interactive Spiral Visualization")

num_points = st.slider('Number of Points in Spiral', min_value=50, max_value=1000, value=500)
num_turns = st.slider('Number of Turns in Spiral', min_value=1, max_value=10, value=5)

spiral_data = generate_spiral(num_points, num_turns)

spiral_df = pd.DataFrame(spiral_data, columns=['x', 'y'])

chart = alt.Chart(spiral_df).mark_circle(size=3).encode(
    x='x',
    y='y'
).properties(
    width=600,
    height=600
)

st.altair_chart(chart, use_container_width=True)
🛠 Create Requirements File
Create a file:

requirements.txt
nginx
Copy
Edit
streamlit
altair
pandas
numpy
🛠 Create the Dockerfile
Create a file:

Dockerfile
Dockerfile
Copy
Edit
# Use Python official image
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy requirements and install dependencies
COPY requirements.txt .
RUN pip install -r requirements.txt

# Copy application code
COPY . .

# Expose Streamlit port
EXPOSE 8501

# Run Streamlit
CMD ["streamlit", "run", "app.py", "--server.port=8501", "--server.address=0.0.0.0"]
🏗️ Build the Docker Image
In your project directory:

bash
Copy
Edit
docker build -t streamlit_spiral .
✅ Output should end with something like:

nginx
Copy
Edit
Successfully tagged streamlit_spiral:latest
📦 Check Image
List all Docker images:

bash
Copy
Edit
docker images
You should see:


REPOSITORY	TAG	IMAGE ID	CREATED	SIZE
streamlit_spiral	latest	abc123	2 min ago	300MB
🚀 Run the Container
Start the app inside a Docker container:

bash
Copy
Edit
docker run -p 8501:8501 streamlit_spiral
-p 8501:8501 → Exposes Streamlit on your local port 8501.

Now open your browser and go to:

bash
Copy
Edit
http://localhost:8501
✅ You should see your interactive Spiral app running!

📋 Final Project Structure
Your directory should look like:

markdown
Copy
Edit
container-experiments/
└── ex1/
    ├── app.py
    ├── Dockerfile
    ├── requirements.txt
