# Use an official Node.js runtime as a parent image
FROM node:14-alpine AS build

# Set the working directory in the container
WORKDIR /app

# Copy package.json and package-lock.json to the working directory
COPY package.json package-lock.json ./

# Install app dependencies
RUN npm install

# Copy the entire frontend code to the working directory
COPY . .

# Build the React app
RUN npm run build

# Use a lightweight web server to serve the built files
FROM nginx:alpine

# Copy the built files from the build stage to the web server's serving directory
COPY --from=build /app/build /usr/share/nginx/html

# Expose port 80
EXPOSE 80

# Start the web server
CMD ["nginx", "-g", "daemon off;"]
