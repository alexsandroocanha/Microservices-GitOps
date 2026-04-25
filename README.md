<h1 align="center">GitOps with Argo CD</h1>
<p align="center"> <i>Configuring access to private repositories and continuous deployment with Argo CD</i></p>

---

### Topics
* [Requirements](#requirements)
* [Initial Setup](#initial-setup)
* [ArgoCD Configuration](#argocd-configuration)
* [ArgoCD Authentication](#argocd-authentication)
* [GitHub Token Creation](#token-creation)
* [Private Repository Configuration](#private-repository-configuration)
* [Deploy Synchronization](#deploy-synchronization)

---

## Requirements

- [x] Docker  
- [x] Chocolatey  
- [x] kubectl  
- [x] ArgoCD CLI  
- [x] Kubernetes Cluster  

---

## Initial Setup

To verify if the cluster is online, open a terminal and run:

```powershell
kubectl get all
````

The next step is to create a namespace for ArgoCD:

```powershell
kubectl create namespace argocd
```

Now we install ArgoCD:

```powershell
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Verify if the pods were created correctly:

```powershell
kubectl get pods -n argocd
```

Expected output:

```powershell
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

## ArgoCD Authentication

Run the following command in a terminal (**keep it open**) and open a second terminal:

```powershell
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

---

ArgoCD generates a random initial password.
To retrieve it on Windows, run:

```powershell
$pw = kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}"
```

```powershell
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($pw))
```

Save this password securely, as it will be used for login.

---

Now log in to ArgoCD:

```powershell
argocd login localhost:8080
```

Accept the prompts and use:

* Username: `admin`
* Password: the decoded value above

---

## Token Creation

If you want to use a private repository, you need to create a GitHub **Fine-grained Personal Access Token**.

---

### Step 1: GitHub Settings

Click your GitHub profile and go to **Settings**

<img 
height="300" 
alt="image" 
src="https://github.com/user-attachments/assets/2c4bdc73-7166-4975-a2f3-50bdd908b059" 
/>

---

Scroll down and find **Developer settings**

<img 
width="200" 
alt="image" 
src="https://github.com/user-attachments/assets/aa903142-4926-47b7-adbf-5399e0e01a6e" 
/>

---

Go to **Personal access tokens → Fine-grained tokens**

<img 
width="250" 
alt="image" 
src="https://github.com/user-attachments/assets/d400e6e2-f882-42cf-868a-90fe9c3818d0" 
/>

---

Click **Generate new token**

<img 
width="500" 
alt="image" 
src="https://github.com/user-attachments/assets/0a35d1aa-d6ab-4432-a911-dcda97ebe4c2" 
/>

---

Configure:

* token name
* select repository access (choose your manifest repository)

<img 
width="400" 
alt="image" 
src="https://github.com/user-attachments/assets/15fdfc0d-4e5e-44de-8a55-bec15c855a95" 
/>

---

Set permissions to **read-only access**

<img 
width="500" 
alt="image" 
src="https://github.com/user-attachments/assets/014dc8a1-0cb2-43f0-b142-3b7303b5ebf8" 
/>

---

Copy the generated token and store it securely.

<img 
width="500" 
alt="image" 
src="https://github.com/user-attachments/assets/866371ff-1d07-4ee7-b3b8-7bf97e9a2d35" 
/>

---

## Private Repository Configuration

Now configure the token inside ArgoCD:

```powershell
argocd repo add <REPO_URL> --username <GITHUB_USERNAME> --password <GITHUB_TOKEN>
```

---

Create the ArgoCD application:

```powershell
argocd app create boutique --repo <REPO_URL> --path . --dest-server https://kubernetes.default.svc --dest-namespace default
```

---

### Notes

* `--path` → folder where manifests are located
* `--dest-server` → Kubernetes cluster endpoint (default for local clusters like Rancher Desktop)

---

Access ArgoCD UI:

```
http://localhost:8080
```

Login:

* username: `admin`
* password: ArgoCD initial password

---

## Deploy Synchronization

After logging into the UI, select your application (e.g. `boutique`).

<img 
width="500" 
alt="image" 
src="https://github.com/user-attachments/assets/a1eccd09-b875-4ad6-85d0-c20ff3a7ba55"
/>

---

Click **SYNC**

<img 
width="500"
alt="image" 
src="https://github.com/user-attachments/assets/eedb6c1c-cca1-485c-bcf5-a5a0ffda71fc" 
/>

---

Then click **SYNCHRONIZE**

<img 
width="500" 
alt="image" 
src="https://github.com/user-attachments/assets/fe13efaa-3afd-4690-8a71-c2eae9c30f42" 
/>

---

Go to **Details**

<img 
width="500" 
alt="image" 
src="https://github.com/user-attachments/assets/463f43c2-7cdf-4f8d-bf8a-ec8619f24faf" 
/>

---

Enable **Sync Policy**

<img 
width="500" 
alt="image" 
src="https://github.com/user-attachments/assets/2eddd6eb-e9e4-4378-83fe-d883591268b4" 
/>

---

Enable both automatic sync options.

---

## Final Result

Your application is now deployed and managed by ArgoCD.

---

## Contact Information

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge\&logo=linkedin\&logoColor=white)](https://www.linkedin.com/in/alexsandro-ocanha-rodrigues-77149a35b/)
[![Instagram](https://img.shields.io/badge/Instagram-E4405F?style=for-the-badge\&logo=instagram\&logoColor=white)](https://www.instagram.com/alexsandro.pcap/)
[![Gmail](https://img.shields.io/badge/Gmail-D14836?style=for-the-badge\&logo=gmail\&logoColor=white)](mailto:alexsandroocanha@gmail.com)
