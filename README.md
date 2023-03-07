<h1 align="center"> ✨ how to install k8s step by step (my research) ✨ </h1> 

[figma file for details](https://github.com/nnbaocuong99/details-k8s-project/tree/main/figma)

---

# ❗️ Part 1: Setup VMBox & Cluster
## ⚒ Setup:

### 1. Install: 
- [Git](https://git-scm.com/)
- [curl](https://curl.se/)
- [Oracle VM VirtualBox](https://www.virtualbox.org/wiki/Downloads) <sup>recommend v6.0. (its easier to setup and provider)</sup>
- [Vagrant by HashiCorp](https://developer.hashicorp.com/vagrant/downloads) 
- On:
  - Window: [Scoop](https://scoop.sh/) <sup>recommend</sup>, [Chocolatey](https://chocolatey.org/) or [winget](https://learn.microsoft.com/en-us/windows/package-manager/winget/)
  - MacOS: [Homebew](https://brew.sh/)

### 2. Create virtual machines: 
- Download 2 files below, re-name to `Vagrantfile`
- Put each into 2 different folders:
   - [Vagrant-master](https://github.com/nnbaocuong99/details-k8s-project/blob/main/document/vagrantfile-master) for the <ins>*master node*</ins>
   - [Vagrant-worker](https://github.com/nnbaocuong99/details-k8s-project/blob/main/document/Vagrantfile-worker) for the <ins>*worker node*</ins>

### 3. Scripts
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
- Get yourself a terminal and ssh into the master-node `ssh username@your_ip_address`
- Optional: if you're using *VScode* or any remote control method, you'll need to add an user. replace `your-username-go-here` with the name you're expecting:
```
$ useradd --comment 'ur-username-go-here' --create-home your-username-go-here --shell /bin/bash
```

### ✏️ <ins>Step 2:</ins>
- Switch to the root user: `$ sudo su` 
- Check if ur docker has been installed yet: `$ docker version` 

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
- Run`$ sudo su` & `$ docker version`
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
## ⚒ On Windows:

### <ins>1. Scoop</ins>
- Open a PowerShell terminal then run:
```
$ Set-ExecutionPolicy RemoteSigned -Scope CurrentUser # Optional: Needed to run a remote script the first time
$ irm get.scoop.sh | iex
```
<div align="center">
    <img src="https://user-images.githubusercontent.com/100349044/222875034-ca5f633f-37ee-4180-ae9d-c37fb248d745.png" alt="uvu" width="800">
    <br>
    <br>
</div>



### <ins>2. curl</ins>
- Download it from official page: [curl - Download](https://curl.se/download.html)
> or
```
$ scoop install curl
```

### <ins>3. Kubectl</ins>
- Basically run command below | [Scoop](https://scoop.sh/) or [Chocolatey](https://community.chocolatey.org/) installed require
```
$ scoop install kubectl
$ choco install kubernetes-cli
```
> or
- Download [latest release](https://dl.k8s.io/release/v1.26.0/bin/windows/amd64/kubectl.exe)
- Create a folder `C:\Program Files` and name it <ins>**KubeTools**</ins>, copy the downloaded file (<ins>**kubectl.exe**</ins>) into it.
- Add the folder location to the Windows PATH environment variable (optional): 
  - Right click `This PC` -> `Properties` -> `Advanced system settings` -> `Environment variables`
  - In the `System variables` choose `Path` -> `Edit` -> `New` -> `C:\Program Files\KubeTools`
- `cd .\KubeTools\` and run the curl command below to download the kubectl command:
```
$ curl -LO https://dl.k8s.io/release/v1.21.0/bin/windows/amd64/kubectl.exe  
```
<div align="center">
    <img src="https://user-images.githubusercontent.com/100349044/222653672-2035576f-732b-4355-9553-a9b08ce9ce46.png" alt="uvu" width="800">
    <br>
    <br>
</div>

### <ins>3. Helm</ins>
- Commands line to install | [Scoop](https://scoop.sh/) or [Chocolatey](https://community.chocolatey.org/) installed require
```  
$ scoop install helm
$ choco install kubernetes-helm
```
- If you prefer Chocolatey here is the [Helm package](https://community.chocolatey.org/packages/kubernetes-helm) build to Chocolatey

<div align="center">
    <img src="https://user-images.githubusercontent.com/100349044/222875084-4fc95ebd-88b4-44e6-8ed1-85ef43c08b26.png" alt="uvu" width="800">
    <br>
    <br>
</div>


## ⚒ On Linux, Ubuntu:

### <ins>1. curl</ins>

- Update the system, get latest stable version:
```
$ sudo apt update
$ sudo apt upgrade
```

- Type the following `apt command` or `apt-get command`
```
$ sudo apt install curl
```

### <ins>2. Kubectl</ins>
- Update your system:
```
$ sudo apt-get update -y
$ sudo apt-get upgrade -y
```

- Download the lastest release:
```
$ curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

- download the checksum file and validate:
```
$ curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
$ echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
```

- Install kubectl
```
$ sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
$ apt-get install -y kubectl 
```

<div align="center">
    <img src="https://user-images.githubusercontent.com/100349044/222874944-c71242a7-d782-4c8e-818f-5f41e1b8c665.png" alt="uvu" width="800">
    <br>
    <br>
</div>


### <ins>3. Helm</ins>
- From the Binary Releases:
  - Download your [desired version](https://github.com/helm/helm/releases)
  - Unpack it `tar -zxvf helm-v3.0.0-linux-amd64.tar.gz`
  - Find the helm binary in the unpacked directory, and move it to its desired destination (mv linux-amd64/helm /usr/local/bin/helm)


- From Apt (Debian/Ubuntu)
```
$ curl https://baltocdn.com/helm/signing.asc | sudo apt-key add -
$ sudo apt-get install apt-transport-https --yes
$ echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
$ sudo apt-get update
$ sudo apt-get install helm
```

- From Script
```
$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
$ chmod 700 get_helm.sh
$ ./get_helm.sh
```
> Helm now has an installer script that will automatically grab the latest version of Helm and install it locally. You can fetch that script, and then execute it locally. It's well documented so that you can read through it and understand what it is doing before you run it.

## ⚒ On MacOS:
- <ins>*On MacOS there is a package manager similar to Scoop or Chocolatey,... on Windows called Homebrew and highly recommended by so many users*</ins>
- You can simply install it on your MacOS by run this command:
```
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
<div align="center">
    <img src="https://user-images.githubusercontent.com/100349044/223048033-004366f1-e936-4579-854e-48810e7c8664.png" alt="uvu" width="600">
    <br>
    <br>
</div>
- And if you choosing others installation method. or the version selection:
  - `amd` for Intel chip
  - `arm` for Apple Silicon chip

### <ins>1. curl</ins>
- Run the command below in the terminal:
```
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" < /dev/null 2> /dev/null
```
- If a password is required after running the command enter your Mac's user password to continue. Wait until the installation finish. 

- Run the command below in the terminal:
```
$ brew install curl
```
<div align="center">
    <img src="https://user-images.githubusercontent.com/100349044/223296377-fa65775b-001d-47ad-84e3-713f05e8e899.png" alt="uvu" width="700">
    <br>
    <br>
</div>



### <ins>2. Kubectl</ins>

- Run the installation command:
```
$ brew install kubectl
$ brew install kubernetes-cli
```

> or

- Download the latest release:
```
$ curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/amd64/kubectl"
```

- Downlad the checksum & validate:
```
$ curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/amd64/kubectl.sha256"
$ echo "$(cat kubectl.sha256)  kubectl" | shasum -a 256 --check
``` 

- Make the kubectl binary executable.
```
$ chmod +x ./kubectl
```

- Move the kubectl binary to a file location on your system PATH.
```
$ sudo mv ./kubectl /usr/local/bin/kubectl
$ sudo chown root: /usr/local/bin/kubectl
```

<div align="center">
    <img src="https://user-images.githubusercontent.com/100349044/223046588-e725db8c-3561-4244-9147-6e4b76e39dbf.png" alt="uvu" width="900">
    <br>
    <br>
</div>

### <ins>3. Helm</ins>
- Install Helm with Homebrew:
```
$ brew install helm
$ brew install kubernetes-helm
```
<div align="center">
    <img src="https://user-images.githubusercontent.com/100349044/223296514-6cd8f144-0090-4ded-92f6-3add3addbe4e.png" alt="uvu" width="600">
    <br>
    <br>
</div>

- From script: 
```
$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
$ chmod 700 get_helm.sh
$ ./get_helm.sh
```

- From Source (also for Linux)
```
$ git clone https://github.com/helm/helm.git
$ cd helm
$ make
```

---

# ❗️ part 3: argocd
## ⚒ Install:

### 1. On Windows:
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

## 2. On Linux:

- Download: 
```
$ curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
```

- Install and remove the temp file:
```
$ sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
$ rm argocd-linux-amd64
```

## 3. On MacOS:
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

- Install the ArgoCD:
```
$ sudo install -m 555 argocd-darwin-amd64 /usr/local/bin/argocd
$ rm argocd-darwin-amd64
```


## ⚒ Setup:
### 1. Install ArgocCD:
```
$ kubectl create namespace argocd
$ kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

<div align="center">
    <img src="https://user-images.githubusercontent.com/100349044/223318199-ee2a4732-5e44-400b-896a-dd7fcb69bf71.png" alt="uvu" width="800">
    <br>
    <br>
</div>


### 2. Expose and port foward
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


### 3. Access
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


### 4. Connect repo & Create application 





















<!--

# ❗️ part 4: [practicing] - deploy a service using helm in

## ⚒ deploy a service with helm

### 1. for example: nginx
```
# To install an NGINX Ingress controller using Helm, first add the nginx-stable repository to helm, then run helm repo update . After we have added the repository we can deploy using the chart nginx-stable/nginx-ingress.
$ helm repo add nginx-stable https://helm.nginx.com/stable
$ helm repo update


# The following command installs the chart with the release name nginx-ingress
$ helm install nginx-ingress nginx-stable/nginx-ingress --set rbac.create=true
```
-->
