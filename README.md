# Ververica Platform & Flink Workshop

## Overview

This repo is designed to be used as the base for demo for any workshop with Ververica Platform and Flink.

### TODO 

- [] Flink jobs
  - [ ] Easily customizable templates
  - [ ] Examples of jobs upgrade

- [] Flink SQL
  - [ ] Datasources for Flink SQL
  - [ ] Flink SQL examples

- [] Monitoring
  - [ ] Prometheus with Grafana
  - [ ] Loki with Promtail to scrap logs

- [] Scripts
  - [ ] Setup Minikube locally
  - [ ] Setup all components
  - [ ] Manage jobs by passing arguments to script

## Setup environment locally

### Requirements

- Installed a hypervisor (like Virtualbox) or a container runtime (like Docker)
- At least 8 GB RAM and 4 CPU cores

### Tutorial

1. Install Minikube.

**MacOS**
```
brew install minikube
```

**Windows**

Chocolatey:
```
choco install minikube
```

PowerShell:
```
New-Item -Path 'c:\' -Name 'minikube' -ItemType Directory -Force
Invoke-WebRequest -OutFile 'c:\minikube\minikube.exe' -Uri 'https://github.com/kubernetes/minikube/releases/latest/download/minikube-windows-amd64.exe' -UseBasicParsing

RUN AS ADMINISTRATOR

$oldPath = [Environment]::GetEnvironmentVariable('Path', [EnvironmentVariableTarget]::Machine)
if ($oldPath.Split(';') -inotcontains 'C:\minikube'){ `
  [Environment]::SetEnvironmentVariable('Path', $('{0};C:\minikube' -f $oldPath), [EnvironmentVariableTarget]::Machine) `
}
```

**Linux**
```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

2. Start Minikube from terminal.
```
minikube start --memory=8G --cpus=4
```

3. Check if the Minikube cluster started correctly.

4. Install Helm

**MacOS**
```
brew install helm
```

**Windows**

Chocolatey
```
choco install helm
```

PowerShell
```
New-Item -Path 'c:\' -Name 'minikube' -ItemType Directory -Force
Invoke-WebRequest -OutFile 'c:\minikube\minikube.exe' -Uri 'https://github.com/kubernetes/minikube/releases/latest/download/minikube-windows-amd64.exe' -UseBasicParsing

RUN AS ADMINISTRATOR

$oldPath = [Environment]::GetEnvironmentVariable('Path', [EnvironmentVariableTarget]::Machine)
if ($oldPath.Split(';') -inotcontains 'C:\minikube'){ `
  [Environment]::SetEnvironmentVariable('Path', $('{0};C:\minikube' -f $oldPath), [EnvironmentVariableTarget]::Machine) `
}
```

**Linux**
```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

5. Create namespaces for Ververica.
```
   kubectl create namespace vvp
   kubectl create namespace vvp-jobs
```

6. Install MinIO as our local blob storage.
``` 
helm repo add stable https://charts.helm.sh/stable
helm --namespace vvp \
    install minio stable/minio \
    --values vvp-values/minio-values.yaml
```

7. Install Ververica Platform.
``` 
helm repo add ververica https://charts.ververica.com
helm --namespace vvp \
    install vvp ververica/ververica-platform \
    --values vvp-values/ververica-values.yaml
```

8. Forward port with Ververica Platform.
``` 
kubectl -n vvp port-forward services/vvp-ververica-platform 8080:80
```

9. Create default deployment target that defines basic deployment target with the name of target namespace (we want to add it in the namespace called vvp-jobs.
``` 
curl localhost:8080/api/v1/namespaces/default/deployment-targets \
    -X POST \
    -H "Content-Type: application/yaml" \
    -H "Accept: application/yaml" \
    --data-binary @deployments/deployment-target.yaml
```

10. Create  first Flink job in Ververica (Top Speed Windowing).
```
    curl localhost:8080/api/v1/namespaces/default/deployments \
        -X POST \
        -H "Content-Type: application/yaml" \
        -H "Accept: application/yaml" \
        --data-binary @deployments/top-speed/deployment.yaml
```
11. Update job by using the following command.
```
    curl localhost:8080/api/v1/namespaces/default/deployments/top-speed-windowing \
        -X PATCH \
        -H "Content-Type: application/yaml" \
        -H "Accept: application/yaml" \
        --data-binary @deployments/top-speed/deployment.yaml
```

## Credits

[Ververica Platform](https://www.ververica.com/platform)
