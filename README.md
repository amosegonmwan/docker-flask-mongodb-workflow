# Flask MongoDB Dockerized Application

This project demonstrates a simple Flask application that connects to a MongoDB instance and is containerized using Docker. The project also includes a GitHub Actions workflow for building and pushing Docker images to Docker Hub.

## Project Structure

### `.github/workflows/build.yaml`

- **name**: The workflow is named `build`.
- **on**: The workflow is triggered manually using `workflow_dispatch`.
- **jobs**:
  - **docker**: This job runs on `ubuntu-latest` and includes the following steps:
    - **Checkout code**: Uses `actions/checkout@v4` to check out the repository code.
    - **Login to Docker Hub**: Uses `docker/login-action@v3` with secrets `DOCKERHUB_USERNAME` and `DOCKERHUB_TOKEN` for Docker Hub login.
    - **Docker metadata**: Uses `docker/metadata-action@v5` to generate metadata for the Docker image, setting the image name to `amosegonmwan/flask`.
    - **Docker Build and push**: Uses `docker/build-push-action@v5` to build the Docker image and push it to Docker Hub, using the tags generated from the previous step.

### `Dockerfile`

- **Base Image**: The Dockerfile uses `python:3.10-alpine` as the base image, optimized for a smaller size.
- **Builder Stage**:
  - The working directory is set to `/src`.
  - The `requirements.txt` file is copied to `/src`.
  - Python dependencies are installed using `pip` with a cache mount to speed up subsequent builds.
  - The entire application code is copied to the image.
  - The application starts with the command `python3 server.py`.
- **Development Environment Stage**:
  - Based on the `builder` stage, this stage installs Git and Docker tools for development.
  - A `vscode` user is created with the `docker` group, and Bash is set as the default shell.
  - Docker CLI, Buildx, and Compose are copied from an external Docker image.
  - The application starts with the command `python3 server.py`.

### `server.py`

- **Flask Application**: The Python script uses Flask to create a simple web server.
- **MongoDB Connection**: Connects to a MongoDB instance running on `mongo:27017` using `pymongo`.
- **Root Endpoint**:
  - The application defines a route `/` that checks the connection to MongoDB.
  - If MongoDB is not available, it returns "Server not available".
  - Otherwise, it returns "Hello from the MongoDB client!".
- **Main Function**:
  - The Flask app is run on host `0.0.0.0` with a port number defined by the `FLASK_SERVER_PORT` environment variable, defaulting to `9090`, and with debugging enabled.

### `requirements.txt`

Lists the Python dependencies required for the Flask application:

- **pymongo**: The official MongoDB driver for Python, enabling MongoDB connectivity.
- **flask**: A lightweight WSGI web application framework in Python, used to create the web server.

## Running the Application

To run the application using Docker, build the Docker image using the Dockerfile provided, then run a container from the image. The Flask app will be accessible on port `9090` by default.

## GitHub Actions Workflow

The included GitHub Actions workflow automates the process of building and pushing Docker images to Docker Hub. It leverages Docker metadata and build-push actions to ensure that images are correctly tagged and pushed to the repository.

## License

This project is licensed under the MIT License.
