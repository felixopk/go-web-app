# Go Web Application

This is a simple website written in Golang. It uses the `net/http` package to serve HTTP requests.

## Running the server

To run the server, execute the following command:

```bash
go run main.go


The server will start on port 8080. You can access it by navigating to `http://localhost:8080/courses` in your web browser.

## Looks like this

![Website](static/images/golang-website.png)

Go Web App Deployment with Helm & GitHub Actions
🚀 Project Overview
This project demonstrates a complete CI/CD pipeline for a Go web application, using:

GitHub Actions for building, testing, linting, and deploying

Helm for Kubernetes deployments

Docker Hub for image hosting

📦 Deployment Steps
1. Install Helm
bash
Copy
Edit
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
2. Deploy with Helm
bash
Copy
Edit
helm upgrade --install go-web-app helm/go-web-app-chart -f helm/go-web-app-chart/values.yaml
3. Verify Deployment
bash
Copy
Edit
kubectl get pods
🛠 Troubleshooting: InvalidImageName in Kubernetes
Problem
During deployment, pods failed with:

bash
Copy
Edit
kubectl get pods
NAME                          READY   STATUS             RESTARTS   AGE
go-web-app-xxxxxxx-xxxxx      0/1     InvalidImageName   0          30s
📸 Screenshot — Issue

Even though the image existed in Docker Hub and could be pulled locally:

bash
Copy
Edit
docker pull felixopk101/go-web-app:16967046981
Cause
The tag value in values.yaml was a large integer:

yaml
Copy
Edit
tag: 16967046981
Helm parsed it as a number and rendered it in scientific notation:

arduino
Copy
Edit
image: felixopk101/go-web-app:1.6967046981e+10
Docker image tags cannot contain this format, leading to InvalidImageName.

Solution
Wrap large tags in quotes:

yaml
Copy
Edit
tag: "16967046981"
Update the GitHub Actions step to preserve quotes:

bash
Copy
Edit
sed -i "s/tag: .*/tag: \"${{ github.run_id }}\"/" helm/go-web-app-chart/values.yaml
Redeploy:

bash
Copy
Edit
helm upgrade --install go-web-app helm/go-web-app-chart -f helm/go-web-app-chart/values.yaml
Verify:

bash
Copy
Edit
kubectl get pods
Should show:

go
Copy
Edit
go-web-app-xxxxxxxxx-xxxxx   1/1     Running
📸 Screenshot — Fixed


