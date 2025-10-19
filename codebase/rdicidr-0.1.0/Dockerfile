# --- STAGE 1: Builder ---
FROM node:15 as builder

# Use a specific directory for the application code
WORKDIR /app

# Copy package files first to leverage Docker cache layers
COPY package.json package-lock.json ./

# Install dependencies. 'npm ci' is used for clean, repeatable installs from package-lock.json
# We install all dependencies, including devDependencies needed for the build step.
RUN npm ci

# Copy the rest of the application source code
COPY . .

# *** CRITICAL CHANGE: Build the React application for production ***
# This command creates the optimized, ready-to-serve files in the 'build' directory.
RUN npm run build


# --- STAGE 2: Release/Production Image (Serving the Static Files) ---
# We use a very minimal image (Node 15-slim) just to run a lightweight server.
FROM node:15-slim as release

# Install a simple, lightweight static file server utility globally
RUN npm install -g serve

# Set the working directory
WORKDIR /app

# Copy the production build output from the builder stage
# The 'build' folder contains all the static HTML/CSS/JS needed to run the app.
COPY --from=builder /app/build ./build

# Define the port the static server will listen on
EXPOSE 8080

# *** CRITICAL CHANGE: Run the static server on the build directory ***
# The 'serve' command runs the compiled app on port 8080.
ENTRYPOINT ["serve", "-s", "build"]
CMD ["-l", "8080"]
