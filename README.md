# Comprehensive Docker Guide for Students

## What is Docker?

Docker is a platform that simplifies application development, deployment, and running through containerization. It uses OS-level virtualization to package applications with their dependencies into standardized units called containers.

### Why Docker Matters
- **Consistency**: Eliminates "it works on my machine" problems
- **Isolation**: Applications run in isolated environments
- **Efficiency**: Uses fewer resources than traditional virtual machines
- **Portability**: Containers can run anywhere Docker is installed
- **Scalability**: Easy to scale applications horizontally

### Docker Architecture
Docker uses a client-server architecture consisting of:
1. **Docker Client**: Command-line interface users interact with
2. **Docker Daemon (dockerd)**: Background service managing Docker objects
3. **Docker Registry**: Repository for Docker images (e.g., Docker Hub)

## Docker Components

### Docker File

A Dockerfile is a text document containing a series of instructions that automate the steps to create a Docker image. Think of it as a recipe for your container.

**Basic Structure:**
```dockerfile
# Base image
FROM ubuntu:20.04

# Set working directory
WORKDIR /app

# Copy application files
COPY . .

# Install dependencies
RUN apt-get update && apt-get install -y python3 python3-pip
RUN pip3 install -r requirements.txt

# Set environment variables
ENV PORT=8080

# Expose port
EXPOSE 8080

# Command to run on container start
CMD ["python3", "app.py"]
```

**Common Dockerfile Instructions:**

| Instruction | Purpose | Example |
|-------------|---------|---------|
| FROM | Specifies base image | `FROM python:3.9-slim` |
| WORKDIR | Sets working directory | `WORKDIR /usr/src/app` |
| COPY/ADD | Copies files from host to container | `COPY . .` |
| RUN | Executes commands during build | `RUN pip install -r requirements.txt` |
| ENV | Sets environment variables | `ENV DEBUG=False` |
| EXPOSE | Documents which ports to publish | `EXPOSE 80` |
| CMD | Default command to run | `CMD ["python", "app.py"]` |
| ENTRYPOINT | Configures container as executable | `ENTRYPOINT ["python"]` |

### Docker Image

A Docker image is a read-only template containing a set of instructions for creating a container. It's like a snapshot of an application and its environment.

**Image Layers:**
Each instruction in a Dockerfile creates a layer in the image. Layers are cached, which makes builds faster when changes are made.

**Image Naming Convention:**
- **Repository**: `username/image-name`
- **Tag**: `username/image-name:tag` (default tag is "latest")

**Example of image layers visualization:**
```
IMAGE          CREATED       CREATED BY                                      SIZE
3af14f5a29d8   4 weeks ago   /bin/sh -c #(nop)  CMD ["python3" "app.py"]    0B
<missing>      4 weeks ago   /bin/sh -c #(nop)  EXPOSE 8080/tcp             0B
<missing>      4 weeks ago   /bin/sh -c pip3 install -r requirements.txt    42MB
<missing>      4 weeks ago   /bin/sh -c apt-get update && apt-get install   75MB
<missing>      4 weeks ago   /bin/sh -c #(nop) COPY . .                     35KB
<missing>      4 weeks ago   /bin/sh -c #(nop) WORKDIR /app                 0B
<missing>      4 weeks ago   /bin/sh -c #(nop)  CMD ["/bin/bash"]           0B
<missing>      4 weeks ago   /bin/sh -c #(nop) ADD file:9ca60...            72.9MB
```

### Docker Container

A container is a runnable instance of an image. It's completely isolated from the host system and other containers. Think of it as a lightweight, standalone executable package.

**Container Lifecycle:**
1. **Created**: Container is created but not started
2. **Running**: Container is running with allocated resources
3. **Paused**: Container execution is paused
4. **Stopped**: Container is stopped but still exists
5. **Deleted**: Container is removed

**Container States Visualization:**
```
┌─────────────┐    start     ┌─────────────┐
│   Created   │─────────────►│   Running   │
└─────────────┘              └──────┬──────┘
                                    │
                                    │ pause
                                    ▼
                             ┌─────────────┐
                             │   Paused    │
                             └──────┬──────┘
                                    │
                                    │ unpause
                                    ▼
┌─────────────┐     stop    ┌─────────────┐
│   Removed   │◄────────────┤   Stopped   │
└─────────────┘             └─────────────┘
```

## Detailed Docker Commands

### Running Containers

#### `docker run` - Create and start a container

```bash
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

**Key Options:**
- `-d, --detach`: Run in background
- `-i, --interactive`: Keep STDIN open
- `-t, --tty`: Allocate a pseudo-TTY
- `-p, --publish`: Map port (host:container)
- `-v, --volume`: Mount volume
- `--name`: Assign name to container
- `--network`: Connect to network
- `--restart`: Restart policy
- `-e, --env`: Set environment variables
- `--memory`: Limit memory
- `--cpus`: Limit CPU usage

**Common Examples:**

1. **Run an interactive container with terminal:**
```bash
docker run -it --name python_dev python:3.9 bash
```

2. **Run a detached web server with port mapping:**
```bash
docker run -d --name web_server -p 8080:80 nginx
```

3. **Run with volume mount:**
```bash
docker run -v $(pwd):/app -w /app python:3.9 python script.py
```

4. **Run with resource limits:**
```bash
docker run --memory="512m" --cpus="0.5" ubuntu:latest stress --cpu 1
```

5. **Run with environment variables:**
```bash
docker run -e DEBUG=true -e PORT=8000 my_app:latest
```

### Container Management

#### `docker ps` - List containers

```bash
docker ps [OPTIONS]
```

**Key Options:**
- `-a, --all`: Show all containers (default shows just running)
- `-q, --quiet`: Only display container IDs
- `--filter`: Filter output based on conditions
- `-s, --size`: Display total file sizes

**Examples:**

1. **List all containers with size:**
```bash
docker ps -as
```

2. **List containers filtered by status:**
```bash
docker ps --filter "status=exited"
```

3. **List containers by name pattern:**
```bash
docker ps --filter "name=web"
```

4. **List containers with custom format:**
```bash
docker ps --format "table {{.ID}}\t{{.Names}}\t{{.Status}}"
```

#### `docker start/stop/restart` - Manage container state

```bash
docker start [OPTIONS] CONTAINER [CONTAINER...]
docker stop [OPTIONS] CONTAINER [CONTAINER...]
docker restart [OPTIONS] CONTAINER [CONTAINER...]
```

**Key Options:**
- `-a, --attach`: Attach to container's STDOUT/STDERR
- `-i, --interactive`: Attach to container's STDIN
- `-t, --time`: Seconds to wait for stop (default: 10)

**Examples:**

1. **Start container interactively:**
```bash
docker start -ai web_app
```

2. **Stop container with timeout:**
```bash
docker stop --time=30 database
```

3. **Restart multiple containers:**
```bash
docker restart web_server db_server cache_server
```

#### `docker exec` - Execute commands in running containers

```bash
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
```

**Key Options:**
- `-d, --detach`: Run in background
- `-i, --interactive`: Keep STDIN open
- `-t, --tty`: Allocate pseudo-TTY
- `-e, --env`: Set environment variables
- `-w, --workdir`: Working directory
- `--user`: Username or UID

**Examples:**

1. **Run interactive shell:**
```bash
docker exec -it postgres_db bash
```

2. **Execute database query:**
```bash
docker exec -i database_container psql -U postgres -c "SELECT * FROM users;"
```

3. **Run as different user:**
```bash
docker exec --user www-data web_server python manage.py migrate
```

4. **Execute commands via shell with pipes:**
```bash
docker exec web_app sh -c "ls -la | grep .py"
```

### Image Management

#### `docker images` - List images

```bash
docker images [OPTIONS] [REPOSITORY[:TAG]]
```

**Key Options:**
- `-a, --all`: Show all images (including intermediates)
- `--digests`: Show digests
- `--filter`: Filter output based on conditions
- `-q, --quiet`: Only show IDs

**Examples:**

1. **List images with their sizes:**
```bash
docker images --format "{{.Repository}}:{{.Tag}} - {{.Size}}"
```

2. **List dangling images:**
```bash
docker images --filter "dangling=true"
```

3. **List images by time:**
```bash
docker images --filter "since=ubuntu:latest"
```

#### `docker build` - Build an image from a Dockerfile

```bash
docker build [OPTIONS] PATH | URL | -
```

**Key Options:**
- `-t, --tag`: Name and optionally tag the image
- `--file`: Specify Dockerfile (default: PATH/Dockerfile)
- `--build-arg`: Set build-time variables
- `--no-cache`: Do not use cache when building the image
- `--pull`: Always attempt to pull a newer version of base images

**Examples:**

1. **Build with tag:**
```bash
docker build -t myapp:1.0 .
```

2. **Build with multiple tags:**
```bash
docker build -t myapp:1.0 -t myapp:latest .
```

3. **Build with custom Dockerfile:**
```bash
docker build -f Dockerfile.dev -t myapp:dev .
```

4. **Build with build arguments:**
```bash
docker build --build-arg VERSION=1.2.3 -t myapp .
```

5. **Build without using cache:**
```bash
docker build --no-cache -t myapp .
```

#### `docker rmi` - Remove images

```bash
docker rmi [OPTIONS] IMAGE [IMAGE...]
```

**Key Options:**
- `-f, --force`: Force removal
- `--no-prune`: Do not delete untagged parents

**Examples:**

1. **Remove multiple images:**
```bash
docker rmi image1:tag1 image2:tag2
```

2. **Remove all dangling images:**
```bash
docker rmi $(docker images -f "dangling=true" -q)
```

3. **Force remove image in use by containers:**
```bash
docker rmi -f python:3.8
```

### Data Management

#### `docker cp` - Copy files between containers and host

```bash
docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-
docker cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH
```

**Key Options:**
- `-a, --archive`: Archive mode (copy all uid/gid information)
- `-L, --follow-link`: Always follow symbol link

**Examples:**

1. **Copy from host to container:**
```bash
docker cp ./config.json my_container:/app/config.json
```

2. **Copy from container to host:**
```bash
docker cp my_container:/var/log/app.log ./logs/
```

3. **Copy directory with archive mode:**
```bash
docker cp -a my_container:/app/data ./backup/
```

4. **Copy using standard input/output:**
```bash
tar -czf - ./src | docker cp - my_container:/app
```

#### `docker volume` - Manage volumes

```bash
docker volume create [OPTIONS] [VOLUME]
docker volume ls [OPTIONS]
docker volume inspect [OPTIONS] VOLUME [VOLUME...]
docker volume rm [OPTIONS] VOLUME [VOLUME...]
docker volume prune [OPTIONS]
```

**Examples:**

1. **Create a named volume:**
```bash
docker volume create app_data
```

2. **Mount a volume in a container:**
```bash
docker run -v app_data:/data myapp
```

3. **List volumes with filter:**
```bash
docker volume ls --filter "dangling=true"
```

4. **Inspect volume details:**
```bash
docker volume inspect app_data
```

5. **Remove unused volumes:**
```bash
docker volume prune --force
```

### Network Management

#### `docker network` - Manage networks

```bash
docker network create [OPTIONS] NETWORK
docker network ls [OPTIONS]
docker network inspect [OPTIONS] NETWORK [NETWORK...]
docker network connect [OPTIONS] NETWORK CONTAINER
docker network disconnect [OPTIONS] NETWORK CONTAINER
docker network rm [OPTIONS] NETWORK [NETWORK...]
docker network prune [OPTIONS]
```

**Examples:**

1. **Create a custom bridge network:**
```bash
docker network create --driver bridge app_network
```

2. **Create network with subnet:**
```bash
docker network create --subnet=172.18.0.0/16 custom_network
```

3. **Connect container to network:**
```bash
docker network connect app_network web_server
```

4. **Disconnect container from network:**
```bash
docker network disconnect app_network web_server
```

5. **Inspect network details:**
```bash
docker network inspect bridge
```

## Docker Compose

Docker Compose is a tool for defining and running multi-container Docker applications.

### Basic Docker Compose File (docker-compose.yml)

```yaml
version: '3'

services:
  web:
    build: ./web
    ports:
      - "8000:8000"
    volumes:
      - ./web:/app
    depends_on:
      - db
    environment:
      - DATABASE_URL=postgres://postgres:password@db:5432/app
      
  db:
    image: postgres:13
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=app

volumes:
  postgres_data:
```

### Common Docker Compose Commands

```bash
# Start services
docker-compose up [OPTIONS] [SERVICE...]

# Start in background
docker-compose up -d

# View logs
docker-compose logs [OPTIONS] [SERVICE...]

# Execute command in service
docker-compose exec [OPTIONS] SERVICE COMMAND [ARGS...]

# Stop services
docker-compose down [OPTIONS]

# List containers
docker-compose ps [OPTIONS] [SERVICE...]

# View resource usage
docker-compose top [SERVICE...]
```

## Practical Use Cases and Examples

### Example 1: Python Web Application

**Dockerfile:**
```dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
```

**app.py:**
```python
from flask import Flask
import os

app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello from Docker!"

if __name__ == '__main__':
    port = int(os.environ.get('PORT', 5000))
    app.run(host='0.0.0.0', port=port)
```

**Build and run:**
```bash
docker build -t python-flask-app .
docker run -d -p 5000:5000 --name web python-flask-app
```

### Example 2: Data Science Environment

**Dockerfile:**
```dockerfile
FROM python:3.9

WORKDIR /notebooks

RUN pip install jupyter pandas numpy matplotlib scikit-learn seaborn

EXPOSE 8888

CMD ["jupyter", "notebook", "--ip=0.0.0.0", "--port=8888", "--no-browser", "--allow-root"]
```

**Build and run:**
```bash
docker build -t datascience-notebook .
docker run -p 8888:8888 -v $(pwd):/notebooks datascience-notebook
```

### Example 3: Multi-container Application with Docker Compose

**docker-compose.yml:**
```yaml
version: '3'

services:
  web:
    build: ./web
    ports:
      - "8000:8000"
    depends_on:
      - db
    environment:
      - DATABASE_URL=mongodb://db:27017/app
    volumes:
      - ./web:/app
    restart: always
    
  db:
    image: mongo:4.4
    volumes:
      - mongodb_data:/data/db
    ports:
      - "27017:27017"
      
  redis:
    image: redis:6.0
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

volumes:
  mongodb_data:
  redis_data:
```

## Best Practices

### Security Best Practices

1. **Use specific versions** of base images instead of `latest`
2. **Run containers as non-root** users when possible
3. **Scan images for vulnerabilities** using tools like Docker Scan
4. **Limit container capabilities** using security options
5. **Use multi-stage builds** to reduce attack surface
6. **Never hardcode secrets** in Dockerfiles or images

### Optimization Best Practices

1. **Leverage build cache** by ordering Dockerfile instructions properly
2. **Use .dockerignore** to exclude unnecessary files
3. **Minimize layer size** by combining commands and cleaning up in the same instruction
4. **Use multi-stage builds** to create smaller images
5. **Choose appropriate base images** (alpine for smaller size)
6. **Set appropriate resource limits** for containers

### Example Optimized Dockerfile:

```dockerfile
# Build stage
FROM python:3.9-slim AS builder

WORKDIR /app

COPY requirements.txt .
RUN pip wheel --no-cache-dir --no-deps --wheel-dir /app/wheels -r requirements.txt

# Final stage
FROM python:3.9-slim

WORKDIR /app

# Create non-root user
RUN adduser --disabled-password --gecos '' appuser

# Copy only wheels from builder stage
COPY --from=builder /app/wheels /wheels
COPY --from=builder /app/requirements.txt .

# Install dependencies
RUN pip install --no-cache /wheels/* \
    && rm -rf /wheels \
    && rm -rf /root/.cache/pip/*

COPY --chown=appuser:appuser . .

# Switch to non-root user
USER appuser

EXPOSE 5000

HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost:5000/health || exit 1

CMD ["python", "app.py"]
```

## Docker in Development vs. Production

### Development Workflow

1. **Use volumes** to mount source code for live reloading
2. **Enable debug options** in applications
3. **Share development tools** in containers
4. **Use Docker Compose** for local multi-container setups

### Production Considerations

1. **Optimize for size and security**
2. **Implement health checks**
3. **Set up proper logging**
4. **Configure for high availability**
5. **Use orchestration tools** like Kubernetes or Docker Swarm

## Troubleshooting Common Issues

### Common Problems and Solutions

1. **Container won't start**
   - Check logs: `docker logs container_name`
   - Verify port conflicts: `netstat -tuln`
   - Check resource limits

2. **Image build fails**
   - Check syntax in Dockerfile
   - Ensure base image is available
   - Look for network issues during package installation

3. **Network connectivity issues**
   - Check network configurations: `docker network inspect`
   - Verify DNS settings
   - Check firewall rules

4. **Volume permissions**
   - Match user IDs between host and container
   - Set appropriate permissions on host directories
   - Use named volumes instead of bind mounts

## Advanced Topics

### Docker Registry

A Docker registry is a storage and content delivery system for Docker images.

**Setting up a private registry:**
```bash
docker run -d -p 5000:5000 --name registry registry:2
```

**Push to private registry:**
```bash
docker tag myimage:latest localhost:5000/myimage:latest
docker push localhost:5000/myimage:latest
```

### Docker Swarm

Docker Swarm is Docker's native clustering and orchestration solution.

**Initialize a swarm:**
```bash
docker swarm init --advertise-addr <MANAGER-IP>
```

**Deploy a service:**
```bash
docker service create --replicas 3 --name web nginx
```

## Code Examples in R and Python

### R Dockerfile Example

```dockerfile
FROM r-base:4.1.0

WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y \
    libcurl4-openssl-dev \
    libssl-dev \
    libxml2-dev \
    && rm -rf /var/lib/apt/lists/*

# Install R packages
RUN R -e "install.packages(c('shiny', 'ggplot2', 'dplyr', 'tidyr'), repos='https://cran.rstudio.com/')"

# Copy application files
COPY app.R .

EXPOSE 3838

CMD ["R", "-e", "shiny::runApp('/app', host='0.0.0.0', port=3838)"]
```

**Example R Shiny app.R:**
```r
library(shiny)
library(ggplot2)

# Define UI
ui <- fluidPage(
  titlePanel("Simple Shiny App in Docker"),
  sidebarLayout(
    sidebarPanel(
      sliderInput("n", "Number of points:", min = 10, max = 500, value = 100)
    ),
    mainPanel(
      plotOutput("scatterPlot")
    )
  )
)

# Define server logic
server <- function(input, output) {
  output$scatterPlot <- renderPlot({
    set.seed(123)
    data <- data.frame(
      x = rnorm(input$n),
      y = rnorm(input$n)
    )
    ggplot(data, aes(x, y)) +
      geom_point(alpha = 0.5) +
      theme_minimal() +
      labs(title = paste(input$n, "random points"))
  })
}

# Run the application
shinyApp(ui = ui, server = server)
```

### Python Data Analysis Example

```dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "data_analysis.py"]
```

**requirements.txt:**
```
pandas==1.4.2
matplotlib==3.5.1
numpy==1.22.3
seaborn==0.11.2
```

**data_analysis.py:**
```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import numpy as np
import os

# Create data directory if it doesn't exist
os.makedirs('output', exist_ok=True)

# Generate sample data
np.random.seed(42)
data = pd.DataFrame({
    'group': np.random.choice(['A', 'B', 'C'], size=100),
    'value': np.random.normal(10, 2, size=100)
})

# Basic statistics
print("Basic Statistics:")
print(data.describe())
print("\nGroup statistics:")
print(data.groupby('group').agg({'value': ['mean', 'std', 'min', 'max']}))

# Create visualizations
plt.figure(figsize=(12, 8))

# Histogram
plt.subplot(2, 2, 1)
sns.histplot(data=data, x='value', hue='group', element='step', stat='density', common_norm=False)
plt.title('Value Distribution by Group')

# Boxplot
plt.subplot(2, 2, 2)
sns.boxplot(data=data, x='group', y='value')
plt.title('Box Plot by Group')

# Violin plot
plt.subplot(2, 2, 3)
sns.violinplot(data=data, x='group', y='value')
plt.title('Violin Plot by Group')

# Scatter plot
plt.subplot(2, 2, 4)
sns.scatterplot(data=data, x=data.index, y='value', hue='group')
plt.title('Values Over Index')

plt.tight_layout()
plt.savefig('output/analysis_results.png')
print("Analysis complete! Results saved to output/analysis_results.png")
```

**Running the container:**
```bash
docker build -t data-analysis .
docker run -v $(pwd)/output:/app/output data-analysis
```

## Additional Resources and Learning Paths

### Official Documentation
- [Docker Documentation](https://docs.docker.com/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [Docker Hub](https://hub.docker.com/)

### Learning Path for Beginners
1. Start with basic container concepts and commands
2. Learn to create custom images with Dockerfiles
3. Explore multi-container applications with Docker Compose
4. Understand networking and volume concepts
5. Practice with real-world examples
6. Learn best practices for security and optimization

### Advanced Learning Path
1. Explore container orchestration (Docker Swarm, Kubernetes)
2. Learn about CI/CD integration with Docker
3. Understand container security in depth
4. Explore monitoring and logging solutions
5. Study Docker internals (namespaces, cgroups)
