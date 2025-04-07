# bootcamp-azure
Todos os projetos para o bootcamp Azure

# Projeto AKS - Microsoft Application Platform 🚀

Este projeto foi desenvolvido durante o curso da DIO em parceria com a Microsoft, com foco na criação e gerenciamento de aplicações usando o Azure Kubernetes Service (AKS).

## 💡 O que foi feito

- Criação de um cluster AKS no Azure
- Deploy de uma aplicação containerizada
- Integração com o Azure Container Registry (ACR)
- Exposição da aplicação via Ingress
- Rollback e escalonamento automático

## 📸 Prints e Comandos

**

---

## 🧰 Pré-requisitos

- Azure CLI instalado e autenticado: `az login`
- Docker instalado e configurado
- Conta no Azure com permissão para criar recursos

---

## 🔧 Etapa 1: Criar o Grupo de Recursos

```bash
az group create --name meuGrupoRecursos --location eastus
```

---

## 🏗️ Etapa 2: Criar o ACR (Azure Container Registry)

```bash
az acr create --resource-group meuGrupoRecursos --name meuRegistroACR --sku Basic
```

---

## 🐳 Etapa 3: Buildar e enviar a imagem Docker para o ACR

```bash
# Login no ACR
az acr login --name meuRegistroACR

# Build da imagem Docker
docker build -t meuRegistroACR.azurecr.io/minhaapp:v1 .

# Push da imagem para o ACR
docker push meuRegistroACR.azurecr.io/minhaapp:v1
```

---

## ☸️ Etapa 4: Criar o Cluster AKS e conectar com o ACR

```bash
az aks create \
  --resource-group meuGrupoRecursos \
  --name meuClusterAKS \
  --node-count 1 \
  --enable-addons monitoring \
  --generate-ssh-keys \
  --attach-acr meuRegistroACR
```

---

## 🧩 Etapa 5: Conectar ao Cluster

```bash
az aks get-credentials --resource-group meuGrupoRecursos --name meuClusterAKS
```

---

## 📦 Etapa 6: Arquivos YAML para Deploy

### **1. Deployment - `deployment.yaml`**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: minhaapp-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: minhaapp
  template:
    metadata:
      labels:
        app: minhaapp
    spec:
      containers:
      - name: minhaapp
        image: meuRegistroACR.azurecr.io/minhaapp:v1
        ports:
        - containerPort: 80
```

---

### **2. Service - `service.yaml`**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: minhaapp-service
spec:
  type: LoadBalancer
  selector:
    app: minhaapp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

---

## 🚀 Etapa 7: Aplicar os arquivos no cluster

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

---

## 🌍 Etapa 8: Verificar IP público e acessar app

```bash
kubectl get service minhaapp-service
```

Aguarde alguns segundos e pegue o IP externo. Acesse no navegador:  
`http://<IP-externo>`

---

## 📸 Sugestão de prints para README

- Tela do Azure Portal com o AKS criado
- Terminal com `az aks get-credentials` e `kubectl apply`
- Resultado do `kubectl get pods` e `kubectl get service`
- Print do navegador com sua aplicação rodando no IP externo

---


## 🤔 Insights e aprendizados

- Entendi como orquestrar contêineres com o Kubernetes de forma escalável.
- Aprendi a importância do Ingress Controller para gerenciamento de rotas HTTP.
- Descobri como o Azure simplifica o processo de deploy e monitoramento de clusters Kubernetes.

## 🔗 Links úteis

- [Repositório base da DIO](https://github.com/digitalinnovationone/Microsoft_Application_Platform)
- [Documentação do AKS](https://learn.microsoft.com/pt-br/azure/aks/)

