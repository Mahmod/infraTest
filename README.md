# Dockerized Flask Application for GKE

This is a simple "Hello, World!" web application built with Python and Flask. It is containerized using Docker and is intended for deployment to Google Kubernetes Engine (GKE).

## Prerequisites

Before getting started, ensure you have the following installed:
*   [Docker](https://docs.docker.com/get-docker/)
*   [Google Cloud CLI (`gcloud`)](https://cloud.google.com/sdk/docs/install) (required for GKE deployment)

## Local Development

You can build and run this application locally using Docker.

### 1. Build the Docker Image

To build the Docker image, run the following command from the root directory of the project:

```bash
docker build -t gke-hello-world .
```

### 2. Run the Docker Container Locally

After the image has been built, you can run it locally on port 8080:

```bash
docker run -d -p 8080:8080 --name local-gke-app gke-hello-world
```

The application will now be accessible at `http://localhost:8080`.

You can test it from your terminal:
```bash
curl http://localhost:8080
```

To stop and remove the local container:
```bash
docker rm -f local-gke-app
```

---

## GKE Deployment Setup

To deploy this application to Google Kubernetes Engine (GKE), follow these generalized steps.

> **Note:** Ensure your `gcloud` CLI is installed and configured before proceeding.

### 1. Authenticate with Google Cloud

Log in to your Google Cloud account and configure your active project:
```bash
gcloud auth login
gcloud config set project YOUR_PROJECT_ID
```
*(Replace `YOUR_PROJECT_ID` with your actual GCP Project ID)*

### 2. Push Image to Artifact Registry

You need to push your local Docker image to Google Artifact Registry so that your GKE cluster can access it.

1.  Create a repository (if you don't have one).
2.  Tag your local image with the Artifact Registry path.
    ```bash
    docker tag gke-hello-world LOCATION-docker.pkg.dev/YOUR_PROJECT_ID/REPOSITORY/gke-hello-world:v1
    ```
3.  Push the tagged image:
    ```bash
    docker push LOCATION-docker.pkg.dev/YOUR_PROJECT_ID/REPOSITORY/gke-hello-world:v1
    ```

### 3. Create a GKE Cluster

Create a Kubernetes cluster in your project using the `gcloud` CLI or the Google Cloud Console.
```bash
gcloud container clusters create my-cluster --zone COMPUTE_ZONE
```

### 4. Create Kubernetes Manifests

You will need to create two Kubernetes manifest files:
*   `deployment.yaml`: Defines how to run the container (using the image you pushed to Artifact Registry).
*   `service.yaml`: Exposes the deployment, often creating a LoadBalancer with an external IP address.

### 5. Deploy to GKE

Ensure `kubectl` is authenticated to your cluster:
```bash
gcloud container clusters get-credentials my-cluster --zone COMPUTE_ZONE
```

Apply the manifests:
```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

Once the service is fully provisioned, you can retrieve the external IP address to access the application publicly.
