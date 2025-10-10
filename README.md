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
* Chocolatey
* Kubectl
* Distribuições leves de Kubernetes (Rancher desktop, minikube, kind, etc...)
* ArgoCD Cli
> Neste repositorio eu utilizei o Rancher desktop, mas nada impede de utilizar outra mini distribuições de kubernetes

## Instalação / Documentação de requisitos
### Chocolatey

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
