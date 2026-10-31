# Neurolink CI/CD Pipeline

Jenkins pipeline that builds and deploys the Neurolink event networking platform
(Python/Flask) to AWS EC2 with Docker, every time code is pushed to `main`.

> This repository holds the pipeline and deployment configuration. The
> application source code is kept in a private repository.

## Pipeline

```
GitHub push → webhook → Jenkins → Checkout → Build → Deploy → Health Check → live on EC2
```

| Stage        | What it does |
|--------------|--------------|
| Checkout     | Pulls the latest commit from GitHub |
| Build        | Builds the app image with Docker Compose |
| Deploy       | Recreates only the app container; the database keeps running |
| Health Check | Calls the app from inside its container for up to 60 seconds and fails the build if it never responds |

## Stack

Jenkins · Docker · Docker Compose · PostgreSQL · Nginx · AWS EC2

## Security

- Database password stored in the Jenkins credentials store and passed in at deploy time; never in the repo
- PostgreSQL is not published to the host network; only the app container can reach it
- App container runs as a non-root user and only listens on localhost; Nginx is the public entry point
- SSH keys for GitHub access held in Jenkins credentials

## Server setup

1. Install Jenkins, Docker and Nginx on the EC2 instance.
2. In Jenkins, add a secret text credential with ID `neurolink-db-password`.
3. In GitHub, add a webhook to `https://<jenkins-host>/github-webhook/` for push events.
4. Copy `nginx/neurolink.conf` into Nginx and run `certbot --nginx` to enable HTTPS.
