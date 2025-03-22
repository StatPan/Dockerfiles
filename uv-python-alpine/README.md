## Docker Hub Image Description: uv-based Python Image (Supports 3.10, 3.11, 3.12)

### Image Overview

This Docker image is built based on uv and is designed to make it easy and fast to use various Python versions. You can freely set the working directory through the `APP_HOME` environment variable, and you can maintain a consistent development and deployment environment.

### Key Features

*   **uv-based**: Provides fast package installation and dependency management.
*   **Various Python Version Support**: You can select and use Python 3.10, 3.11, and 3.12 versions.
*   **Flexible Working Directory Setting**: You can easily change the working directory through the `APP_HOME` environment variable.
*   **Easy to Use**: A virtual environment is automatically activated when the Docker image is executed.

### How to Use

1.  **Docker Image Pull**

    ```bash
    docker pull statpan/uv-python:3.10  # Python 3.10
    docker pull statpan/uv-python:3.11  # Python 3.11
    docker pull statpan/uv-python:3.12  # Python 3.12
    ```

    Pull the image corresponding to the desired Python version.

2.  **Docker Container Execution**

    ```bash
    docker run -it -v $(pwd):/app statpan/uv-python:3.10 /bin/sh
    ```

    *   `-it`: Executes the container in interactive mode.
    *   `-v $(pwd):/app`: Mounts the current directory to the `/app` directory of the container.
    *   `statpan/uv-python:3.10`: The Docker image name to use.
    *   `/bin/sh`: The shell to execute within the container.

3.  **`APP_HOME` Environment Variable Setting (Optional)**

    You can change the working directory by setting the `APP_HOME` environment variable.

    ```bash
    docker run -it -e APP_HOME=/custom/path -v $(pwd):/custom/path statpan/uv-python:3.10 /bin/sh
    ```

4.  **Virtual Environment Activation Confirmation**

    When you connect to the container, the virtual environment is automatically activated. You can check the virtual environment name `(.venv)` in the shell prompt.

    ```
    root@<container_id>:/app$
    ```

5.  **Package Installation and Execution**

    ```bash
    uv pip install <package_name>
    python your_script.py
    ```

### Dockerfile Description

```dockerfile
FROM alpine:latest
LABEL description="uv-based Python image (by version), can be changed according to app_home"

# Set environment variables
ARG PYTHON_VERSION=3.10
ENV APP_HOME="/app"

# Install required packages
RUN apk add --no-cache curl

# Install uv
RUN curl -LsSf https://astral.sh/uv/install.sh | sh
ENV PATH="/root/.local/bin:$PATH"

WORKDIR ${APP_HOME}
RUN pwd
USER root

ENV PS1='\u@\h:\w\$ '
ENV PATH="${APP_HOME}/.venv/bin:$PATH"

RUN uv python install ${PYTHON_VERSION}
RUN uv python pin ${PYTHON_VERSION}
RUN uv venv .venv
RUN uv init

CMD ["/bin/sh", "-c", "source ${APP_HOME}/.venv/bin/activate && exec /bin/sh"]
```

*   `FROM alpine:latest`: Uses Alpine Linux as the base image.
*   `LABEL description`: Adds a description to the image.
*   `ARG PYTHON_VERSION`: A build argument that specifies the Python version.
*   `ENV APP_HOME`: An environment variable that sets the working directory.
*   `RUN apk add --no-cache curl`: Installs curl.
*   `uv 설치`: Executes the uv installation script and adds the uv execution path to PATH.
*   `WORKDIR ${APP_HOME}`: Sets the working directory.
*   `USER root`: Sets the user permission to root.
*   `ENV PS1`: Sets the shell prompt.
*   `RUN uv python install ${PYTHON_VERSION}`: Installs the specified Python version.
*   `RUN uv python pin ${PYTHON_VERSION}`: Pins the Python version.
*   `RUN uv venv .venv`: Creates a virtual environment.
*   `RUN uv init`: Initializes uv settings.
*   `CMD`: Sets the command to execute when the container starts. Activates the virtual environment and executes the shell.

### Precautions

*   When using the Docker image, you must select the required Python version correctly.
*   If you change the `APP_HOME` environment variable, you must also change the mount path when executing the container.

### Additional Information

*   uv: [https://github.com/astral-sh/uv](https://github.com/astral-sh/uv)
*   Alpine Linux: [https://www.alpinelinux.org/](https://www.alpinelinux.org/)