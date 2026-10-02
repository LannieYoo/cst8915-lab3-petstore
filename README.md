# CST8915 Lab 3: Deploying the Algonquin Pet Store on Azure PaaS

- **Student Name**: Hye Ran Yoo
- **Student ID**: 041145212
- **Course**: CST8915 Full-stack Cloud-native Development
- **Semester**: Fall 2026

---

## Demo Video

🎥 [Watch Demo Video](https://youtu.be/b6dU2aRcD2o)

---

## Deployment

| Service | Azure service | Code |
|---|---|---|
| RabbitMQ | Virtual Machine (`rabbitmq-vm`) | Lab 1 install script |
| order-service | Web App (`order-service-app`, Node.js) | my repository |
| product-service | Web App (`product-service-app`, Python) | my repository |
| store-front | Static Web Apps (`store-front-app`) | my repository |

---

## Service Repositories

- **order-service**: [LannieYoo/cst8915-lab3-order-service](https://github.com/LannieYoo/cst8915-lab3-order-service)
- **product-service**: [LannieYoo/cst8915-lab3-product-service](https://github.com/LannieYoo/cst8915-lab3-product-service)
- **store-front**: [LannieYoo/cst8915-lab3-store-front](https://github.com/LannieYoo/cst8915-lab3-store-front)

`order-service` and `store-front` are copies of my Lab 2 repositories. They keep the same commits. `product-service` is a new repository, because I changed the service from Rust to Python.

---

## Reflection Questions

### 1. What challenges did you encounter when configuring environment variables in the GitHub Actions workflow?

- **Two different places.** For `order-service` and `product-service` (Web Apps), I put the environment variables in the App settings in the Azure portal. This was easy. The `store-front` (Static Web Apps) was different: it has no App settings for the build, so the API URLs must go in the GitHub Actions workflow file.
- **Why the workflow file.** The `store-front` is a Vue.js app. GitHub Actions builds it into static files, and the browser runs the files. The URLs are put into the files at build time, so they must be known when the build runs.
- **Azure makes the file.** Azure creates the workflow file and commits it to my repository. I had to run `git pull` before I could edit it.
- **YAML spacing.** The `env:` block must start at the same column as `runs-on:` under `build_and_deploy_job`.
- **No `/` at the end of the URLs.** The code already adds `/products` and `/orders`.
- **A change needs a new build.** To change a URL, I must commit again, so GitHub Actions builds the app again. A browser refresh is not enough.

### 2. How does deploying microservices on Azure Web App Service differ from running them locally?

- **No machine to set up.** On my laptop and in Lab 2, I installed Node.js or Rust and opened ports. On Web App Service, I only connect the GitHub repository in the Deployment Center.
- **Push to deploy.** GitHub Actions builds and deploys the code every time I push.
- **Azure gives the URL.** Each service gets an HTTPS `*.azurewebsites.net` URL. I do not open any port for the services.
- **Config in App settings.** I set `PORT` and `RABBITMQ_CONNECTION_STRING` in the Web App settings, not in a `.env` file. The code did not change.
- **Only some runtimes.** Web App Service does not support Rust, so `product-service` was rewritten in Python.
- **The first deployment failed.** Azure made a new identity for GitHub Actions, and its permission was not ready yet. The job worked when I ran it again.

### 3. Why is it important to use environment variables for configurations in a cloud environment?

- **Same code, different environments.** On my laptop, `product-service` uses port 3030. On Azure, the port comes from the App settings.
- **Secrets are not in the code.** The RabbitMQ connection string has a public IP and a password. It is in the Azure settings, not in GitHub.
- **Change the value, not the code.** If RabbitMQ moves to another VM, I only change the connection string.
- **12-Factor App, Config factor.** The configuration is stored in the environment, and the code stays the same.

---

## Notes

- `product-service` uses the Python version provided by the professor. It has the same `/products` endpoint and the same product list as the Rust version.
- Port 5672 on the RabbitMQ VM is open only to the outbound IP addresses of `order-service-app`.
- Both Web Apps use one App Service plan (Free F1).
