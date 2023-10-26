summary: Lab 1 - Container Intro (GitHub Edition)
id: iac-container-github-intro
categories: containers
tags: containers, introduction, exoscale
status: Published
authors: Thomas Schuetz

# Container Technologies - Lab 1 - Introduction (GitHub Edition)

<!-- ------------------------ -->

## What You’ll Learn

In this lab, you will:

- Create your first Dockerfile
- Build and push this Container locally
- Run the Container locally
- Build and push the Container in CI
- Run the Container

### Prerequisites

- Container Engine (Docker Desktop, Podman Desktop or Rancher Desktop) is installed
- Git is installed
- You have a GitHub Account
- You installed an IDE (VSCode, IntelliJ, ...)
- Your repository is already created

## Create your Dockerfile
In this step, you will check out the repository, open it in your IDE and create a very simple Dockerfile.

### Clone the Repository
- Clone the repository to your local machine: `git clone <repository-url>`
- Open the repository in your IDE

### Create a Dockerfile
Before we start creating Dockerfiles, we should be familiar with the syntax. Therefore, we will create a very simple Dockerfile, which will just print "Hello World" when run and simply run it

- Create a new file called `Dockerfile` in the root of your repository
- Add the following content to the file:

```dockerfile
FROM docker.io/library/debian:bookworm-slim

RUN apt-get update && apt-get install -y curl

CMD ["curl", "https://www.fh-burgenland.at"]
```

This Dockerfile will use the official Debian Bookworm image, install curl and run curl against the website of the University of Applied Sciences Burgenland.

### Build and run the Dockerfile
Now it's time to build and run the Dockerfile. This can be done with the following commands:
- `docker build -t hello-world:latest .`

This will build the Dockerfile and tag it with the name `hello-world` and the tag `latest`. The `.` at the end of the command tells Docker to use the current directory as build context.

Note that this command won't push the image to a registry. It will just build it locally.

Nevertheless, as docker stores the images in a local cache, you can run the image with the following command:

- `docker run hello-world:latest`

<aside class="positive">
Your image should now be running and you should see the output of the curl command. Note that it stops after the command is finished. With this, you learned that it's necessary to run a process in the foreground to keep the container running.
</aside>

## Create a simple application
The last example was very easy, but it didn't really show the power of Docker. Therefore, we will create a simple application in Go, which will run in a container.

- Create a new directory called `src` in your repository
- Create a new file called `main.go` in this directory
- Add the following content to the file:

```go
package main

import (
    "fmt"
    "net/http"
)

func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Hello, %q", r.URL.Path)
    })
    http.ListenAndServe(":8080", nil)
}
```

This application is a very simple webserver, which will return the URL path of the request. 

## Create a Dockerfile to build the application
Probably, you are not a Go Developer and you don't have a Go environment installed. Therefore, we will use a Dockerfile to build the application.

- Create a new file called `Dockerfile.build` in the root of your repository
- Add the following content to the file:

```dockerfile
FROM docker.io/library/golang:1.21.3-alpine3.18

COPY src /src
WORKDIR /src

RUN go mod init hello-world \
    && go build -o /app .

CMD /app
```

This Dockerfile will use the official Go image, copy the source code to the image, build the application and run it. 

## Build and run the Dockerfile
Now it's time to build and run the Dockerfile. This can be done with the following commands:
- `docker build -t hello-fhb:latest -f Dockerfile.build .`

This will build the Dockerfile and tag it with the name `hello-world` and the tag `latest`. The `.` at the end of the command tells Docker to use the current directory as build context.

Now we can simply run the image with the following command:
- `docker run -p 8080:8080 hello-fhb:latest`
- Open your browser and navigate to `http://localhost:8080/hello-fhb`

By using `-p 8080:8080` you tell Docker to map the port 8080 of the container to the port 8080 of your local machine, therefore you should be able to access the application via localhost:8080 when it's running.

<aside class="positive">
Your image should now be running and when browsing to the URL, you should see the URL path in the browser. Note that you need to CTRL-C to stop the container now
</aside>

### Automating the build
As you can see, the build process is quite simple, but it might only work on your machine. To make this reproducible and running on every push, we will automate the build process with GitHub Actions.

### Push the current state to GitHub
To have the code available on GitHub, please push the current state to GitHub:
- `git add .`
- `git commit -m "Added Dockerfile and simple Go application"`
- `git push`

When you now open your repository on GitHub, you should see the new files, but nothing is happening yet.

## Run the build in pull-requests when pushing
To start developing your new feature now, check out a new branch:
- `git checkout -b feature/add_ci_build`

Now, we will create a new GitHub Action, which will build the application and push it to the GitHub Container Registry. To do this, we will create a new file called `.github/workflows/build.yml` with the following content:

```yaml
name: Build

on:
  pull_request:
    branches:
      - 'main'

jobs:
  build_image:
    name: Build Container Image
    runs-on: ubuntu-22.04

    steps:
      - name: Check out code
        uses: actions/checkout@8ade135a41bc03ea155e62e844d188df1ea18608 # v4

      - name: Set up Docker Buildx
        id: buildx
        uses: docker/setup-buildx-action@f95db51fddba0c2d1ec667646a06c2ce06100226 # v3

      - name: Build Docker Image
        uses: docker/build-push-action@0565240e2d4ab88bba5387d719585280857ece09 # v5
        with:
          context: .
          platforms: linux/amd64
          file: ./Dockerfile.build
          tags:
            hello-fhb:dev
          push: false
```

You can temporarily change the trigger to on push to your branch to test it.

This GitHub Action will run on every pull-request to the main branch and will build the Dockerfile. It will also tag the image with `hello-fhb:dev`, but don't push it to the registry.

Finally, push the changes to GitHub.

## Push the image to the registry when merging
Now, we will create a new GitHub Action, which will build the application and push it to the GitHub Container Registry. To do this, we will create a new file called `.github/workflows/push.yml` with the following content:

```yaml
name: Build

on:
  push:
    branches:
      - 'main'

jobs:
  build_image:
    name: Build Container Image
    runs-on: ubuntu-22.04

    permissions: 
      contents: read
      packages: write

    steps:
    - name: Check out code
      uses: actions/checkout@8ade135a41bc03ea155e62e844d188df1ea18608 # v4

    
    - name: 'Login to GitHub Container Registry'
      uses: docker/login-action@v1
      with:
        registry: ghcr.io
        username: ${{github.actor}}
        password: ${{secrets.GITHUB_TOKEN}}
    
    - name: Set up Docker Buildx
      id: buildx
      uses: docker/setup-buildx-action@f95db51fddba0c2d1ec667646a06c2ce06100226 # v3
    
    - name: Build Docker Image
      uses: docker/build-push-action@0565240e2d4ab88bba5387d719585280857ece09 # v5
      with:
        context: .
        platforms: linux/amd64
        file: ./Dockerfile.build
        tags: 
          ghcr.io/${{github.repository}}/hello-fhb:${{ github.sha }}
        push: true
```

This GitHub Action will run on every push to the main branch and will build the Dockerfile. It will also tag the image with `ghcr.io/${{github.repository}}/hello-fhb:${{ github.sha }}` and push it to the registry.

Also push the changes to GitHub, try to make some changes, create a pull-request and merge it. You should now see the image in the GitHub Container Registry.

## Create a GitHub Token and log in with docker (if you want)
- Navigate to your personal account settings https://github.com/settings/profile
- Settings -> Developer Settings

  - Personal Access Token
  - Click "Generate new token"
  - Choose a name: `ghcr-token`
  - Choose Permissions:
    - `read:packages`
  - Generate Token
  - Copy the Token to your clipboard

- Log in to the GitHub Container Registry
  - `docker login ghcr.io -u {{your-github-username}} -p {{your-github-token}}`

## Run the container on your machine
Finally, we will run the container on your machine. To do this, we will use the following command:
- `docker run -p 8080:8080 ghcr.io/{{your-github-username}}/hello-fhb:{{your-github-sha}}`

If you can access the application via `http://localhost:8080/hello-fhb`, you have successfully completed the workshop. Congratulations!




