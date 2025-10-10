<h1 align="center">GitOps com Argo CD</h1>
<p align="center"> <i>Configurando acesso a repositório privado e deploy contínuo com Argo CD</i></p>

## Visão Geral

**Propósito**

Exercitar práticas cloud-native em um app realista de loja virtual. 


**Arquitetura**

11 microserviços se comunicando via gRPC (e HTTP no frontend). Cada serviço é independente e versionado/implantado separadamente.


<img width="3556" height="1954" alt="image" src="https://github.com/user-attachments/assets/1387160e-887e-40b4-85ef-ddac0154b8bc" />

## Como isso se conecta ao seu GitOps (Argo CD)

* Repositório: aponte o app do Argo CD para a pasta de manifests (ex.: k8s/ ou online-boutique/kubernetes-manifests).
* Credenciais: repo privado = cadastrar PAT read-only ou SSH Deploy Key no argocd repo add (já fizemos).
* Acesso: acesso web utilizando o Traefik (http://shop.localtest.me)
* Fluxo GitOps:
  * Commit/push de manifests no GitHub;
  * Argo CD detecta diffs → sync (auto-sync opcional com --auto-prune --self-heal);


## Requisitos 
* Docker
* Chocolatey
* Kubectl
* Distribuições leves de Kubernetes (Rancher desktop, minikube, kind, etc...)
* ArgoCD Cli
> Neste repositorio eu utilizei o Rancher desktop, mas nada impede de utilizar outra mini distribuições de kubernetes

## Instalação / Documentação de requisitos
### Chocolatey
> Caso utilize Linux você pode ignorar este passo
> 
Em windows 11, você precisara abrir o terminal em Administrador e executar este comando 
```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

Para verificar se instalou execute este comando
```powershell
choco --version
```

Caso tenha algum problema na instalação ou em alguma outra parte consulte o site oficial do Chocolatey Software

[![Clique aqui](https://img.shields.io/badge/Clique%20aqui-1f6feb?style=for-the-badge)](https://chocolatey.org/install)


### Docker

Nos sistemas windows basta instalar o docker pelo proprio site da Docker logo abaixo

[![Clique aqui](https://img.shields.io/badge/Clique%20aqui-1f6feb?style=for-the-badge)](https://www.docker.com/get-started/)


Caso você tenha selecionado o outro modo de container, basta ir em Troobleshooting, e em Factory Reset


<img width="1444" height="839" alt="image" src="https://github.com/user-attachments/assets/17dbdb6c-1c51-4dd1-a4e6-b15e3e3b11a5" />
<img width="1186" height="360" alt="image" src="https://github.com/user-attachments/assets/6515b186-6759-4c3b-aeef-f30523f4bb39" />


### Kubectl

Em sistemas windows, uma das formas "mais faceis" de se obter o kubectl, é utilizando o chocolatey.

> Para instalar o kubectl no windows, você precisa abrir um terminal em modo de Adminstrador e executar este comando

```powershell
choco install kubernetes-cli
```

Para outras formas de instalação, ou caso utilize ambiente baseado em Linux, consulte a documentação do kubernetes logo abaixo

[![Clique aqui](https://img.shields.io/badge/Clique%20aqui-1f6feb?style=for-the-badge)](https://kubernetes.io/docs/tasks/tools/)


### Rancher Desktop

É até que simples, só basta baixar e instalar a versão msi

Após executar o programa pela primeira vez, basta executar nesta configuração
<img width="515" height="494" alt="image" src="https://github.com/user-attachments/assets/7beda2b2-77f8-44ec-96df-abd0579ca8a3" />

### ArgoCD Cli

Para instalar o ArgoCD eu segui estes passo a passo.
```powershell
Invoke-WebRequest -Uri "https://github.com/argoproj/argo-cd/releases/latest/download/argocd-windows-amd64.exe" -OutFile "argocd.exe"
```
```powershell
Move-Item .\argocd.exe "C:\Windows\System32\argocd.exe"
```
Caso queira instalar de outra maneira, fique avontade de consultar a documentação de instalação do ArgoCD

[![Clique aqui](https://img.shields.io/badge/Clique%20aqui-1f6feb?style=for-the-badge)](https://argo-cd.readthedocs.io/en/stable/cli_installation/)

## 
