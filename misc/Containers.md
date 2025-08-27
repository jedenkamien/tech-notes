## An exercise with Azure Containers - running a simple containerized app
This input is based on https://learn.microsoft.com/en-us/training/modules/publish-container-image-to-azure-container-registry/6-build-run-image-azure-container-registry.

#### 1. Create a resource group.
```bash
labuser-39773601 [ ~ ]$ az group create --name az204-acr-rg --location polandcentral
```

#### 2. Create a container registry inside this resource group.
```bash
labuser-39773601 [ ~ ]$ az acr create --resource-group az204-acr-rg --name majkontejnerredzistri --sku Basic
```

#### 3. Create a simple Dockerfile.
```bash
labuser-39773601 [ ~ ]$ echo FROM mcr.microsoft.com/hello-world > Dockerfile
```

#### 4. Build an image based on this Dockerfile inside the created container registry.
```bash
labuser-39773601 [ ~ ]$ az acr build --image sample/hello-world:v1 --registry majkontejnerredzistri --file Dockerfile .
```

Input:
```bash
- image:
	registry: majkontejnerredzistri.azurecr.io
	repository: sample/hello-world
	tag: v1
	digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
	registry: mcr.microsoft.com
	repository: hello-world
	tag: latest
	digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  git: {}
```

#### 5. View the tag of the created image - confirming it exists.
```bash
labuser-39773601 [ ~ ]$ az acr repository show-tags --name majkontejnerredzistri --repository sample/hello-world --output table
```

Result:
```bash
--------
v1
```

#### 6. Run the application stored in this container.
```bash
labuser-39773601 [ ~ ]$ az acr run --registry majkontejnerredzistri --cmd '$Registry/sample/hello-world:v1' /dev/null
```

Output:
```bash
Unable to find image 'majkontejnerredzistri.azurecr.io/sample/hello-world:v1' locally
v1: Pulling from sample/hello-world
1b930d010525: Pulling fs layer
1b930d010525: Verifying Checksum
1b930d010525: Download complete
1b930d010525: Pull complete
Digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
Status: Downloaded newer image for majkontejnerredzistri.azurecr.io/sample/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
	(amd64)
 3. The Docker daemon created a new container from that image which runs the
	executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
	to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

2024/04/16 12:10:56 Successfully executed container: acb_step_0

```
