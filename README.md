<h1 align="center"> ✨ how to install k8s step by step (my research) ✨ </h1> 

[Figma](https://github.com/nnbaocuong99/details-k8s-project/tree/main/figma)(WIP): Explaination

[Error/Re-search](https://github.com/nnbaocuong99/details-k8s-project/tree/main/error-report): Error while doing the project

---

# ❗️ Part 1: Setup VMBox & Cluster
## ⚒ Setup

### 1. Install✨
- [Git](https://git-scm.com/)
- [curl](https://curl.se/)
- [Oracle VM VirtualBox](https://www.virtualbox.org/wiki/Downloads) <sup>recommend v6.0. (its easier to setup and provider)</sup>
- [Vagrant by HashiCorp](https://developer.hashicorp.com/vagrant/downloads) 
- On:
  - Window: [Scoop](https://scoop.sh/) <sup>recommend</sup>, [Chocolatey](https://chocolatey.org/) or [winget](https://learn.microsoft.com/en-us/windows/package-manager/winget/)
  - MacOS: [Homebew](https://brew.sh/)

### 2. Create virtual machines✨ 
- Download 2 files below, re-name to `Vagrantfile`
- Put each into 2 different folders:
   - [Vagrant-master](https://github.com/nnbaocuong99/details-k8s-project/blob/main/document/vagrantfile-master) for the <ins>*master node*</ins>
   - [Vagrant-worker](https://github.com/nnbaocuong99/details-k8s-project/blob/main/document/Vagrantfile-worker) for the <ins>*worker node*</ins>

### 3. Scripts✨
- Open 2 terminals each separate folders:
> This is my directory
```
C:\Users\spagbo\Desktop\vmb\master>
C:\Users\spagbo\Desktop\vmb\node>
```
- Run this command in the terminals you've been opened ealier:
```
$ vagrant up
```
<div align="center">
    <img src="https://user-images.githubusercontent.com/100349044/222043942-022f202d-065d-49fb-a8b4-8d390b9720f4.png" alt="uvu" width="800">
    <br>
    <br>
</div>


## ⚒ Install Rancher & K8s:

### ✏️ <ins>Step 1:</ins>
- Get yourself a terminal and ssh into the master-node `ssh username@your_ip_address` when its successfully started
- Linux and MacOS has their own terminals, On Windows you can use [PowerShell](https://learn.microsoft.com/en-us/powershell/) or basically just [Terminal](https://apps.microsoft.com/store/detail/windows-terminal/)


### ✏️ <ins>Step 2:</ins>
- Switch to the root user: `sudo su` 
- Check if ur docker has been installed yet: `docker version` 

### ✏️ <ins>Step 3:</ins>
- Choose a tag on [rancher/rancher Tags](https://hub.docker.com/r/rancher/rancher/tags)
- Replace `tag` with the one you've been choosing
```
$ docker run -d --name=rancher-server --restart=unless-stopped -p 80:80 -p 443:443 --privileged rancher/rancher:tag
```
- Wait until the image successfully pulled then get the container id
```
$ docker ps
$ sudo docker ps -aqf "name=containername"`

----------------
# explaination
-a: all.       | Works even if your container is not running.
-q: quietmode. | Output only display numeric container IDs.
-f: filter.    | Filter output based on conditions provided.
```

### ✏️ <ins>Step 4:</ins>
- Access: https://192.168.56.200 or https://192.168.56.200/g 
- Use the container id, replace `container id` in the command below to get the password
```
$ docker logs  container-id  2>&1 | grep "Bootstrap Password:"
```
- Copy the <ins>**red-line**</ins> key/code, thats the password. 
- Login to the Rancher `admin / password`
- Choose the <ins>**custom**</ins> mode:
<div align="center">
    <img src="https://user-images.githubusercontent.com/100349044/222045349-2a57a602-aa60-4621-ba25-01b6fb166668.png" alt="uvu" width="800">
    <br>
    <br>
</div>

- Set a name then click next, tick on the `etcd` and `control panel` it will show you a script:
<div align="center">
    <img src="https://user-images.githubusercontent.com/100349044/222045781-fed45b98-6a0a-4dc0-ae70-7daa7db23490.png" alt="uvu" width="800">
    <br>
    <br>
</div>

### ✏️ <ins>Step 5:</ins> 
- Copy it and add `--address your_worker_IP` before the `--etcd` and you'll get the final script like this:
```
$ sudo docker run -d --privileged --restart=unless-stopped --net=host -v /etc/kubernetes:/etc/kubernetes -v /var/run:/var/run  rancher/rancher-agent:v2.7-091ed163cc5c53efc50bd1a580cb4e54fa097e82-head --server https://192.168.56.200/ --token p5zcnnpcb5cx8pg89vkk5nkx8gbzltk9wbkmfjp6rsn9n6kf729vjp --ca-checksum 37bde28c0dc9fbd360146f727ff4b1cd254d9f17490789f93775fb2ce15b58da --address your_worker_IP --etcd --controlplane --worker
```

### ✏️ <ins>Step 6:</ins>
- SSH into the worker-node: `ssh username@your_ip_address`
- Run`sudo su` & `docker version`
- Run the copied script 
- Get into the masternode and copy the config file. Its look like this:

<div align="center">
    <img src="https://user-images.githubusercontent.com/100349044/222042743-1cf31c2b-ed01-4eb1-8ef9-45a4de0dd9b4.png" alt="uvu" width="1000">
    <br>
    <br>
</div>


### ✏️ <ins>Step 6:</ins>
- Copy to clipboard or save that config to `.txt` if you need it.
- Paste the config to <ins>**default Kubeconfig**</ins> file connect to the cluster
- Default Kubeconfig location in:

<div align="center">
  1. Linux, Ubuntu:
</div>

<div align="center">
    <img src="https://user-images.githubusercontent.com/100349044/222038197-c05c1c93-b440-4b14-afcf-cbb43a33a7d8.png" alt="uvu" width="200">
    <br>
    <br>
</div>

<div align="center">
  2. Windows:
</div>

> you need to open `C:\Users\%USERNAME%` and make a folder `.kube` and create a file name `config` 

<div align="center">
    <img src="https://user-images.githubusercontent.com/100349044/222041299-5ed55daf-ec51-4cbe-a973-96cc6b7123a7.png" alt="uvu" width="300">
    <br>
    <br>
</div>


<div align="center">
  3. MacOS:
</div>

<div align="center">
    <img src="https://user-images.githubusercontent.com/100349044/222041470-074745c4-39a7-432c-bf3b-3c7ec75ba988.png" alt="uvu" width="200">
    <br>
    <br>
</div>


---


# ❗️ Part 2: install tools to work with k8s
## ⚒ Guides:
## Installation docs for:
- [Linux, Ubuntu](https://github.com/nnbaocuong99/details-k8s-project/tree/main/Installations-Docs/Linux%2C%20Ubuntu)
- [Windows](https://github.com/nnbaocuong99/details-k8s-project/tree/main/Installations-Docs/Windows)
- [MacOS](https://github.com/nnbaocuong99/details-k8s-project/tree/main/Installations-Docs/MacOS)

---

# ❗️ part 3: argocd
## ⚒ Install:

### 1. On Windows✨
- Open a **Powershell**
- Grab a version and download: (replace `$version` with the specific version)
```
$version = (Invoke-RestMethod https://api.github.com/repos/argoproj/argo-cd/releases/latest).tag_name
```

- Then run:
```
$url = "https://github.com/argoproj/argo-cd/releases/download/" + $version + "/argocd-windows-amd64.exe"
$output = "argocd.exe"

Invoke-WebRequest -Uri $url -OutFile $output
```
- Add it into `Windows PATH`

## 2. On Linux✨

- Download: 
```
$ curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
```

- Install and remove the temp file:
```
$ sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
$ rm argocd-linux-amd64
```

## 3. On MacOS✨
- Download the latest version with Homebrew:
```
$ brew install argocd
```

- Download the specific version With curl:
```
$ VERSION=$(curl --silent "https://api.github.com/repos/argoproj/argo-cd/releases/latest" | grep '"tag_name"' | sed -E 's/.*"([^"]+)".*/\1/')
$ curl -sSL -o argocd-darwin-amd64 https://github.com/argoproj/argo-cd/releases/download/$VERSION/argocd-darwin-amd64
```
> Replace `VERSION` in the command below with the version of Argo CD you would like to download

- Install:
```
$ sudo install -m 555 argocd-darwin-amd64 /usr/local/bin/argocd
$ rm argocd-darwin-amd64
```


## ⚒ Setup:
### 1. Install ArgocCD✨
```
$ kubectl create namespace argocd
$ kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

<div align="center">
    <img src="https://user-images.githubusercontent.com/100349044/223318199-ee2a4732-5e44-400b-896a-dd7fcb69bf71.png" alt="uvu" width="800">
    <br>
    <br>
</div>


### 2. Expose and port foward✨
- Change the ArgoCD-server service type to LoadBalancer:
```
$ kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```
<div align="center">
    <img src="https://user-images.githubusercontent.com/100349044/223318334-d9d6ba33-d86a-4f4f-9bb8-0fd26dfe0477.png" alt="uvu" width="800">
    <br>
    <br>
</div>


- Run the port forward command to expose the services:
```
$ kubectl port-forward svc/argocd-server -n argocd 8080:443
```
<div align="center">
    <img src="https://user-images.githubusercontent.com/100349044/223347531-b88c2478-a3e7-422c-bb2c-fcffa8de5ad2.png" alt="uvu" width="500">
    <br>
    <br>
</div>


### 3. Access✨
- Once the command exposed, you can access to https://localhost:8080 and it will require to login with `admin` and `password`
<div align="center">
    <img src="https://user-images.githubusercontent.com/100349044/223347770-c13a1d22-3f17-4bec-9f88-cbdced92a4db.png" alt="uvu" width="1000">
    <br>
    <br>
</div>


- Back to local terminal and get argocd password (**based64** installed recommend) by run the command
```
$ kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

<div align="center">
    <img src="https://user-images.githubusercontent.com/100349044/223346388-26210ddf-a562-4a62-95d5-fd89dda63c8e.png" alt="uvu" width="800">
    <br>
    <br>
</div>

- In this case, this is the result
> username: `admin` | password: `6vH7QkjCQFiPPHPZ`

<div align="center">
    <img src="https://user-images.githubusercontent.com/100349044/223376752-e0cc22de-5383-4307-9afc-370050b4e4c1.png" alt="uvu" width="1000">
    <br>
    <br>
</div>

<!--
### 4. Connect repo & Create application✨ 
-->

---


# ❗️ part 4: CI/CD
> <ins>First of all, check [this](https://www.geeksforgeeks.org/what-is-ci-cd/)</ins> 
- *In this part I'll show my result and how to setup also finish the CI/CD project. Lets go!*


## ⚒ CI
### ✏️ <ins>Step 1:</ins> 

- Create and account on [Gitlab](https://gitlab.com/)
> *Quicknote*: You totally can use others platform like Github or what ever, but in this case i highly recommend to use Gitlab because the CI/CD tool from Gitlab is extremely easy to use, all you need to do is create a file in the root location of your repository called `.gitlab-ci.yml`. This file is basically a recipe for how Gitlab should execute pipelines.
- Create a repository, push your content or you can use my content by clone this project.
  - Github: `git clone https://github.com/nnbaocuong99/details-k8s-project.git`
  - Gitlab: `git cloen https://gitlab.com/nnbaocuong99/k8s.git`
- If you're already cloned this project but there is no file called `.gitlab-ci.yml` Create one and copy this content below into it. It will automatically start a `Pipelines` in your next commit.

```
stages:
  - build

build-image:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  rules:
  before_script:
    - docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWD
  script:
    - >
      docker build
      --build-arg DOCKER_USERNAME=$DOCKER_USERNAME
      --build-arg DOCKER_PASSWD=$DOCKER_PASSWD
      -t $DOCKER_USERNAME/demo-gitlabci:1.0 .
    - docker push $DOCKER_USERNAME/demo-gitlabci:1.0
```
### ✏️ <ins>Step 2:</ins> 
- In the Repo: `Settings` -> `CI/CD` -> `Variables` -> `Add Variables`
- Variables:
  - `$DOCKER_USERNAME` = your Docker username
  - `$DOCKER_PASSWD`= your Docker password

<div align="center">
    <img src="https://user-images.githubusercontent.com/100349044/224215639-21e60eda-d930-456c-bdbd-969c0e46d1cf.png" alt="uvu" width="734">
    <br>
    <br>
</div>


### 2. Install Gitlab-runner and Register a runner
- Install Gitlab-runner

## ⚒ CD




