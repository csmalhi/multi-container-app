# syntax=docker/dockerfile:1

# Define the Node version we want
ARG NODE_VERSION=19.5.0

# Use an NVIDIA CUDA base image with Ubuntu 22.04 so that nvidia-smi is available
FROM nvidia/cuda:11.8.0-base-ubuntu22.04

# Install prerequisites, Node.js, and Python
RUN apt-get update && \
    apt-get install -y curl gnupg build-essential python3 python3-pip && \
    curl -fsSL https://deb.nodesource.com/setup_19.x | bash - && \
    apt-get install -y nodejs && \
    rm -rf /var/lib/apt/lists/*

# Install PyTorch and torchvision for testing GPU access
RUN pip3 install torch torchvision

# Set production environment and make GPUs visible
ENV NODE_ENV=production
ENV NVIDIA_VISIBLE_DEVICES=all

WORKDIR /usr/src/app

# Copy package files first to leverage Docker cache
COPY package*.json ./
RUN npm ci --include=dev

# Install nodemon globally for hot-reloading
RUN npm install -g nodemon

# Copy the rest of the source code
COPY . .

# Create a non-root user "node" and set proper ownership
RUN useradd -m node && chown -R node /usr/src/app
USER node

# Expose the port that the application listens on
EXPOSE 3000

# Start the app in development mode
CMD ["npm", "run", "dev"]
