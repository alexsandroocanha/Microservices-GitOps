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
* Kubectl
* Distribuições leves de Kubernetes (Rancher desktop, minikube, kind, etc...)
* ArgoCD Cli
> Neste repositorio eu utilizei o Rancher desktop, mas nada impede de utilizar outra mini distribuições de kubernetes

## Instalação / Documentação de requisitos
### 
