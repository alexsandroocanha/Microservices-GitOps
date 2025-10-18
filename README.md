<h1 align="center">GitOps com Argo CD</h1>
<p align="center"> <i>Configurando acesso a repositório privado e deploy contínuo com Argo CD</i></p>

### Como o ArgoCD se conecta ao seu Repositorio

* Repositório: aponte o app do Argo CD para a pasta de manifests (ex.: k8s/ ou online-boutique/kubernetes-manifests).
* Credenciais: repo privado = cadastrar PAT read-only ou SSH Deploy Key no argocd repo add.
* Acesso: acesso web utilizando o Traefik (http://shop.localtest.me)
* Fluxo GitOps:
  * Commit/push de manifests no GitHub;
  * Argo CD detecta diffs → sync (auto-sync opcional com --auto-prune --self-heal);
### Topicos
* [Requisitos](#requisitos)
* [Instalação dos Requisitos](#instalação)
* [Configuração do ArgoCD](#configuração-inicial-do-argocd)
* [Autenticação do ArgoCD](#agora-vamos-autenticar-o-argocd)
* [Criação do Token GitHub](#criação-do-token)
* [Configuração dos Repositorios](#configuração-dos-repositorios-privados)
* [Sincronizando o Deployment](#sincronizando-o-deployment)

## Requisitos 
- [x] Docker
- [x] Chocolatey
- [x] Kubectl
- [x] Distribuições leves de Kubernetes (Rancher desktop, minikube, kind, etc...)
> Neste repositorio eu utilizei o Rancher desktop, mas nada impede de utilizar outra mini distribuições de kubernetes
- [x] ArgoCD Cli


## Instalação

* [Instalação do Chocolatey](#chocolatey)
* [Instalação do Docker](#docker)
* [Instalação do Kubectl(Kubernetes)](#kubectl)
* [Instalação do Rancher Desktop](#rancher-desktop)
* [Instalação do ArgoCD Cli](#argocd-cli)

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
---

### Docker

Nos sistemas windows basta instalar o docker pelo proprio site da Docker logo abaixo

[![Clique aqui](https://img.shields.io/badge/Clique%20aqui-1f6feb?style=for-the-badge)](https://www.docker.com/get-started/)
---

### Kubectl

Em sistemas windows, uma das formas "mais faceis" de se obter o kubectl, é utilizando o chocolatey.

> Para instalar o kubectl no windows, você precisa abrir um terminal em modo de Adminstrador e executar este comando

```powershell
choco install kubernetes-cli
```

Para outras formas de instalação, ou caso utilize ambiente baseado em Linux, consulte a documentação do kubernetes logo abaixo

[![Clique aqui](https://img.shields.io/badge/Clique%20aqui-1f6feb?style=for-the-badge)](https://kubernetes.io/docs/tasks/tools/)
---

### Rancher Desktop

É até que simples, só basta baixar e instalar a versão msi

[![Clique aqui](https://img.shields.io/badge/Clique%20aqui-1f6feb?style=for-the-badge)](https://rancherdesktop.io/)

Após executar o programa pela primeira vez, basta executar nesta configuração

<img 
 width="200" 
 alt="image" 
 src="https://github.com/user-attachments/assets/7beda2b2-77f8-44ec-96df-abd0579ca8a3" 
/>

Caso você tenha selecionado o outro modo de container, basta ir em Troobleshooting, e em Factory Reset

<img 
 width="400" 
 alt="image" 
 src="https://github.com/user-attachments/assets/17dbdb6c-1c51-4dd1-a4e6-b15e3e3b11a5" 
/>

<img 
 width="500"
 alt="image" 
 src="https://github.com/user-attachments/assets/6515b186-6759-4c3b-aeef-f30523f4bb39"
/>

---

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

---

## Configuração inicial do ArgoCD

Para verificar se o cluster esta online, abra o terminal e execute este comando

```powershell
kubectl get all
```

O proximo passo agora é criar um Namespace para o ArgoCD
```powershell
kubectl create namespace argocd --save-config
```

Agora é aonde realmente baixamos e instalamos o ArgoCD
```powershell
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Agora vamos verificar se os pods foram criados corretamentes
```powershell
kubectl get pods -n argocd
```

A saida deve ser algo parecido com isto
```poweshell
NAME                                                READY   STATUS    RESTARTS   AGE
argocd-application-controller-0                     1/1     Running   0          115s
argocd-applicationset-controller-5f67f4c987-vdtpr   1/1     Running   0          117s
argocd-dex-server-5859d89dcc-c69fx                  1/1     Running   0          117s
argocd-notifications-controller-75c986587-7jznn     1/1     Running   0          116s
argocd-redis-74c8c9c8c6-mzdlv                       1/1     Running   0          116s
argocd-repo-server-76f77874d7-8qscp                 1/1     Running   0          116s
argocd-server-64d5654c48-tkv65                      1/1     Running   0          116s
```

---
### Agora vamos autenticar o argoCD

Execute este comando em um terminal, NÃO FECHE ESTE TERMINAL, e abra outro
```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
<br>

O argoCD gera sempre uma chave aleatoria a cada instalação, para obter estas chaves no windows você tera que executar estes 2 comandos
```powershell
$pw = kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}"
```
```powershell
 [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($pw))
```


Salve este codigo que apareceu especificamente para você em um bloco de notas, pois precisaremos para fazer o Login do ArgoCD.

Após isso execute o seguinte commando
```powershell
argocd login localhost:8080
```
Concorde com os termos, e em username digite admin e em password digite aquele codigo que apareceu para você

---
## Criação do Token

Agora se por acaso você quiser colocar um repositorio privado, você tera que ir no GitHub e criar um Fine-grained tokens
<br>

### Primeiro você clica na sua foto do github e vai em settings
<img 
 height="300" 
 alt="image" 
 src="https://github.com/user-attachments/assets/2c4bdc73-7166-4975-a2f3-50bdd908b059" 
/>
<br>

Após isso escrola para baixo até achar esta opção "Developer settings"
<br>
<img 
 width="200" 
 alt="image" 
 src="https://github.com/user-attachments/assets/aa903142-4926-47b7-adbf-5399e0e01a6e" 
/>
<br>

Após isso clique no botão "Personal acces tokens" e dentro dele, clique na opção "Fine-grained tokens"
<br>
<img 
 width="250" 
 alt="image" 
 src="https://github.com/user-attachments/assets/d400e6e2-f882-42cf-868a-90fe9c3818d0" 
/>
<br>

Clique no botão ao lado "Generate new token"
<br>
<img 
 width="500" 
 alt="image" 
 src="https://github.com/user-attachments/assets/0a35d1aa-d6ab-4432-a911-dcda97ebe4c2" 
/>
<br>

Agora é só colocar o nome, e logo abaixo selecionar esta opção e selecionar o repositorio privado que contem os manifestos

<br>
<img 
 width="400" 
 alt="image" 
 src="https://github.com/user-attachments/assets/15fdfc0d-4e5e-44de-8a55-bec15c855a95" 
/>
<br>

Para finalizar, coloque estas configurações apenas para a leitura

<br>
<img 
 width="500" 
 alt="image" 
 src="https://github.com/user-attachments/assets/014dc8a1-0cb2-43f0-b142-3b7303b5ebf8" 
/>
<br>

Copie o token e anote em um bloco de notas

<br>
<img 
 width="500" 
 alt="image" 
 src="https://github.com/user-attachments/assets/866371ff-1d07-4ee7-b3b8-7bf97e9a2d35" 
/>
<br>

---
## Configuração dos repositorios privados

Agora, voltando para o terminal. Configuraremos o token dentro do ArgoCD

```powershell
argocd repo add LINK-DO-REPOSITORIO --username SEU-USUARIO-DO-GITHUB --password SEU-TOKEN
```

Agora configuraremos o deploy do manifesto
```powershell
argocd app create botique --repo LINK-DO-REPOSITORIO --path . --dest-server https://kubernetes.default.svc --dest-namespace default
```
Algumas observações
> --path - É aonde se localiza o manifesto, pense que ele é uma pessoa e ira fazer o deploy. Você precisa se direcionar para o manifesto colocando o caminho da pasta (Exemplo: /repositorio-legal/manifestos/)

> --dest-server - É o cluster kubernetes que você vai fazer o deploy. Caso esteja utilizando o Rancher Desktop o cluster dele é este por default, caso tenha utilizando outro metodo você tera que adicionar o cluster no argocd

Agora teremos que acessar a interface grafica em "localhost:8080"

Nesta parte segue a mesma ideia do login por cli, usuario "admin" e a senha é aquele token que o argocd gera

---
## Sincronizando o Deployment

### Após logar na interface grafica, aparecera esta tela, clicaremos no seu app, no caso a "botique"
<br>
<img 
 width="500" 
 alt="image" 
 src="https://github.com/user-attachments/assets/a1eccd09-b875-4ad6-85d0-c20ff3a7ba55"
/>



### Nesta tela, clicaremos em SYNC
<br>
<img 
 width="500"
 alt="image" 
 src="https://github.com/user-attachments/assets/eedb6c1c-cca1-485c-bcf5-a5a0ffda71fc" 
/>

### E em SYNCHRONIZE
<br>
<img 
 width="500" 
 alt="image" 
 src="https://github.com/user-attachments/assets/fe13efaa-3afd-4690-8a71-c2eae9c30f42" 
/>


### Agora vamos em details
<br>
<img 
 width="500" 
 alt="image" 
 src="https://github.com/user-attachments/assets/463f43c2-7cdf-4f8d-bf8a-ec8619f24faf" 
/>

### Iremos em SYNC POLICY, ativar e habilitar esta opção
<br>
<img 
 width="500" 
 alt="image" 
 src="https://github.com/user-attachments/assets/2eddd6eb-e9e4-4378-83fe-d883591268b4" 
/>

Após isso habilite as 2 opções que apareceu dentro de SYNC POLICY
E pronto, projeto deployado e finalizado com argoCD

### Informações para Contato

[![Linkedin](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/alexsandro-ocanha-rodrigues-77149a35b/)
[![Instagram](https://img.shields.io/badge/Instagram-E4405F?style=for-the-badge&logo=instagram&logoColor=white)](https://www.instagram.com/alexsandro.pcap/)
[![Gmail](https://img.shields.io/badge/Gmail-D14836?style=for-the-badge&logo=gmail&logoColor=white)](mailto:alexsandroocanha@gmail.com)
