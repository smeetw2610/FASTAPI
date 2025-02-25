# Create Docker image of fastapi api server using github actions

## Python Server

FastAPI is a modern, fast (high-performance) web framework for building APIs with Python 3.7+.

### main.py
python
from fastapi import FastAPI
import uvicorn

app = FastAPI()

@app.get("/")
def read_root():
    return dict(name="Smeet", Location="Dehradun")

@app.get("/{data}")
def read_data(data):
    return dict(hi=data, Location="Dehradun")

if __name__ == "__main__":
    uvicorn.run("main:app", host="0.0.0.0", port=80, reload=True)


### Python Requirements
requirements.txt
text
fastapi
uvicorn


## Dockerfile for Image Building / Containerization of App

Dockerfile
dockerfile
FROM ubuntu

RUN apt update -y
RUN apt install python3 python3-pip pipenv -y

WORKDIR /app
COPY . /app/
RUN pipenv install -r requirements.txt

EXPOSE 80

CMD pipenv run python3 ./main.py



## GitHub Actions for Docker Image Creation

.github/workflows/DockerBuild.yml
yaml
name: Docker Image Build and Push

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps: 
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      - name: Log in to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_USERNAME }}  
          password: ${{ secrets.DOCKER_PASSWORD }}  

      - name: Build and push Docker image
        uses: docker/build-push-action@v4
        with:
          context: .
          push: true
          tags: smeet26/fastapi:latest



## Docker Token
To generate a token for a Docker registry, follow these steps based on your specific registry type:

### Docker Hub (Public Registry)
Docker Hub uses Personal Access Tokens (PATs) instead of passwords for authentication.

#### Steps:
1. Go to [Docker Hub](https://hub.docker.com/)
2. Sign in with your Docker account.
3. Click on your profile (top-right corner) → Account Settings.
4. Navigate to *Security → Access Tokens*.
5. Click *Generate Token*.
6. Give it a name, set the permissions, and click *Generate*.
7. Copy the token (it will not be shown again).

![Example Image](https://github.com/smeetw2610/FASTAPI/blob/main/1.png)

#### Use the Token for Login:
sh
docker login -u <your-docker-username> 

Then, enter the token when prompted.

![Example Image](https://github.com/smeetw2610/FASTAPI/blob/main/2.png)

#### Add the Repository secret:
Go to the Settings--> Secrets and Variables--> Actions--> Create New Repository Secrets contaning the Docker Hub username and Docker Hub password that contains the Token generated

![Example Image](https://github.com/smeetw2610/FASTAPI/blob/main/3.png)

#### Steps to push the .github folder and files in your GitHub Repository
1. Go to the Terminal and run the command to initialize git
sh
git init

![Example Image](https://github.com/smeetw2610/FASTAPI/blob/main/4.png)

2. Add your remote repository
sh
git remote add origin <your-github-repo-url>

![Example Image](https://github.com/smeetw2610/FASTAPI/blob/main/5.png)

3. Add files for the commit
sh
git add file-name

![Example Image](https://github.com/smeetw2610/FASTAPI/blob/main/6.png)

4. Commit the changes
sh
git commit -m "Added files"


5. Push the files to github
sh
git push -u origin main

![Example Image](https://github.com/smeetw2610/FASTAPI/blob/main/7.png)

#### Check that the image has been build or not?
1. Go to the Repository--> Actions Tab. You will see that 1 workflow will be running. Wait unitl it build successfully.

![Example Image](https://github.com/smeetw2610/FASTAPI/blob/main/8.png)

![Example Image](https://github.com/smeetw2610/FASTAPI/blob/main/9.png)

2. Now check you Docker Hub account. The images will be created in your account.

![Example Image](https://github.com/smeetw2610/FASTAPI/blob/main/10.png)

---
