# Use official Node.js image to build the frontend
FROM node:18 AS builder

# Set working directory
WORKDIR /app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the frontend code
COPY . .

# Build the frontend
RUN npm run build

# Use Nginx as the base image for serving static files
FROM nginx:alpine

# Copy the build output from the builder stage to Nginx's default directory
COPY --from=builder /app/dist /usr/share/nginx/html

# Expose the frontend port
EXPOSE 80

# Start Nginx
CMD ["nginx", "-g", "daemon off;"]

