# ROS 2 Jazzy Development Container

This repository provides relatively minimal setup for a ROS 2 Jazzy development environment using Docker.

The Container addresses the unusual decision to hardcode the default user in the Jazzy base image to UID/GID 1000 (username "ubuntu") This container allows use of your Local Machine user account running as your host user to ensure seamless file permissions in the workspace.

This is intended to be compatible with both VS Code Dev Containers and command-line usage.

## Prerequisites

- **Docker**: Install Docker and Docker Compose on your system.
- **VS Code** (optional): Install Visual Studio Code with the Dev Containers extension (`ms-vscode-remote.remote-containers`).
- **Ubuntu Host**: The setup is tested on Ubuntu with ROS 2 Jazzy, but it should work on other Linux distributions.
- **X11 Display**: Ensure you have an X11 display server running (e.g., for RViz). Set `DISPLAY` environment variable if needed.

## Repository Structure

```
JazzyDocker/
├── .devcontainer/
│   └── devcontainer.json
├── Dockerfile
├── docker-compose.yml
└── README.md
```

## Setup Instructions

### Option 1: Using VS Code Dev Containers

1. **Clone the Repository**:
   ```bash
   git clone <repository-url> JazzyDocker
   cd JazzyDocker
   ```

2. **Open in VS Code**:
   ```bash
   code .
   ```

3. **Reopen in Container**:
   - Press `Ctrl+Shift+P` (or `Cmd+Shift+P` on macOS) to open the Command Palette.
   - Select **Dev Containers: Reopen in Container**.
   - VS Code will build the container using `docker-compose.yml` and `Dockerfile`, generating an `.env` file with your user’s UID, GID, and username.

4. **Verify Setup**:
   - Open a terminal in VS Code (`Terminal > New Terminal`).
   - Run:
     ```bash
     whoami
     id
     ```
   - Ensure the output matches your host user (e.g., `jack`, `uid=1000(jack) gid=1000(jack)`).
   - Check the ROS 2 environment:
     ```bash
     ros2 --version
     ```

### Option 2: Using Command Line

1. **Clone the Repository**:
   ```bash
   git clone <repository-url> JazzyDocker
   cd JazzyDocker
   ```

2. **Create .env File**:
   Create a file named `.env` in the `JazzyDocker` directory with your user’s details:
   ```env
   UID=$(id -u)
   GID=$(id -g)
   USERNAME=$(whoami)
   ```

3. **Build and Run**:
   ```bash
   docker compose up --build
   ```

4. **Access the Container**:
   - Attach to the running container:
     ```bash
     docker exec -it jazzy_ros bash
     ```
   - Verify the user and ROS 2 setup:
     ```bash
     whoami
     id
     ros2 --version
     ```

### Notes

- **Workspace**: The repository mounts the current directory as `/jazzy_ws/src` in the container. Place your ROS 2 packages in the `src/` directory.
- **Permissions**: The container runs as your host user, ensuring files created in `/jazzy_ws/src` have correct ownership.
- **Customization**: Add additional ROS 2 packages to the `Dockerfile`’s `apt-get install` section as needed.
- **Troubleshooting**:
  - If the build fails, clear the Docker cache:
    ```bash
    docker builder prune
    docker compose build --no-cache
    ```
  - Check build logs for errors in the user setup (`Configuring user...`).

## License

MIT License. See [LICENSE](LICENSE) for details.
