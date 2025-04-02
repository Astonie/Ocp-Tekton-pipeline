
# Ocp-Tekton-pipeline Project

This project is a simple web application served using an Nginx Docker container. It showcases a personal profile page for Komlan Florient Dogbe, a DevOps & SRE Expert.

## Project Structure

- **index.html**: The main HTML file containing the profile page content.
- **Dockerfile**: The Docker configuration file used to build the Nginx container.

## Getting Started

### Prerequisites

- Docker installed on your local machine.

### Building the Docker Image

To build the Docker image, run the following command in the project directory:

```bash
docker build -t my-nginx-app .
```

### Running the Docker Container

To run the Docker container, use the following command:

```bash
docker run -d -p 80:80 my-nginx-app
```

This will start the Nginx server and serve the profile page on `http://localhost`.

## Accessing the Application

Once the container is running, open your web browser and navigate to `http://localhost` to view the profile page.

## Resources

- [Nginx Documentation](https://nginx.org/en/docs/)
- [Docker Documentation](https://docs.docker.com/)

## Author

Komlan Florient Dogbe - [LinkedIn](https://de.linkedin.com/in/komlan-florient-dogbe-redhat-architect) | [GitHub](https://github.com/florient2016)

Feel free to contribute to this project by submitting issues or pull requests.
