
# Use an official Python runtime as a parent image
FROM python:3.9-slim

# Set the working directory in the container
WORKDIR /app

# Copy requirements.txt into the container
COPY requirements.txt ./

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy the rest of the backend code into the container
COPY . .

# Make port 5001 available to the world outside this container
EXPOSE 5001

# Define environment variables
ENV PYTHONUNBUFFERED=1

# Run app.py when the container launches
CMD ["python", "app.py"]
