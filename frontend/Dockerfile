# Use the Node.js 20 base image
FROM node:20-buster as build

# Set the working directory
WORKDIR /app

# Copy the package.json and package-lock.json
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application files
COPY . .

# Build the application with the legacy OpenSSL provider
RUN npm run build

# Use a smaller base image for serving the built application
FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html

# Expose the port that your app runs on
EXPOSE 80

# Start the server
CMD ["nginx", "-g", "daemon off;"]

