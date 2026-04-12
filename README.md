# DEISI App Template

<p align="center">
  Desenvolvido por<br>
  <strong>Afonso Sá</strong> e <strong>Lucas Martins</strong><br>
  Engenharia Informática – Universidade Lusófona<br>
  Projeto Final (TFC)
</p>

Template base para adicionar novas aplicações ao cluster K3s do DEISI com:

* Docker
* Helm
* GitHub Actions
* Deploy automático para o namespace `deisi`

---

## 📁 Estrutura do Projeto

```text
.
├── .github/workflows/deploy.yml
├── helm/app/
│   ├── Chart.yaml
│   ├── values.yaml
│   └── templates/
│       ├── deployment.yaml
│       ├── service.yaml
│       └── ingress.yaml
├── Dockerfile
└── README.md
```

---

## 🚀 Como usar este template

### 1. Criar novo repositório

Duplicar este repositório para a nova aplicação.

---

### 2. Substituir placeholders

Substituir nos ficheiros:

* `USERNAME`
* `APP_NAME`

Locais principais:

* `.github/workflows/deploy.yml`
* `helm/app/values.yaml`

---

### 3. Ajustar o Dockerfile

O `Dockerfile` incluído é apenas um exemplo para aplicações Node.js.

Cada aplicação deve adaptar este ficheiro conforme a sua tecnologia (Django, Spring, etc.).

---

### 4. Ajustar configuração Helm

No ficheiro `helm/app/values.yaml`, rever:

* nome da aplicação
* imagem
* porta
* host do ingress
* recursos (CPU / memória)

---

### 5. Criar GitHub Secrets

No repositório da nova aplicação:

**Settings → Secrets and variables → Actions**

Criar os seguintes secrets:

---

#### 🔑 GHCR_TOKEN

Token de acesso ao GitHub Container Registry (GHCR), necessário para:

* fazer push da imagem Docker
* autenticar o GitHub Actions

##### Como criar:

1. Ir a:
   https://github.com/settings/tokens

2. Clicar em:
   **"Generate new token (classic)"**

3. Selecionar permissões:

* `write:packages`
* `read:packages`

4. Gerar o token e copiar

5. No repositório:

   * Nome: `GHCR_TOKEN`
   * Valor: (colar o token)

---

#### ☸️ KUBECONFIG

Ficheiro de acesso ao cluster Kubernetes (K3s), necessário para:

* permitir que o GitHub Actions faça deploy no cluster

##### Como obter:

No servidor (VM onde está o K3s), executar:

```bash
cat /etc/rancher/k3s/k3s.yaml
```

Copiar TODO o conteúdo.

---

##### ⚠️ IMPORTANTE

Antes de colocar no GitHub:

* substituir `127.0.0.1` pelo IP público da VM

Exemplo:

```yaml
server: https://134.122.74.17:6443
```

---

##### Depois:

No GitHub:

* Nome: `KUBECONFIG`
* Valor: colar TODO o ficheiro YAML

---

#### 📌 Resumo

| Secret     | Para que serve               |
| ---------- | ---------------------------- |
| GHCR_TOKEN | Push da imagem Docker        |
| KUBECONFIG | Deploy no cluster Kubernetes |

---

### 6. Fazer deploy

Fazer push para a branch `main`.

A pipeline irá automaticamente:

1. construir a imagem Docker
2. enviar para o GHCR
3. fazer deploy no cluster com Helm

---

## ⚙️ Configuração do Cluster

* Namespace: `deisi`
* Ingress controller: `traefik`
* Image pull secret necessário: `ghcr-secret`

---

## 🌐 Exemplo de Configuração

### Antes (template)

* `USERNAME` = `USERNAME`
* `APP_NAME` = `APP_NAME`

---

### Depois (exemplo real)

* `USERNAME` = `afonso-saaa`
* `APP_NAME` = `mentorias`

---

### Imagem

```text
ghcr.io/afonso-saaa/mentorias
```

---

### Host

```text
mentorias.134.122.74.17.nip.io
```

---

## ⚠️ Ativação do Deploy Automático

Este template utiliza `workflow_dispatch` por defeito para evitar execuções automáticas antes da configuração da aplicação.

Após criar uma nova aplicação a partir deste template e configurar:

* `USERNAME`
* `APP_NAME`
* GitHub Secrets (`GHCR_TOKEN` e `KUBECONFIG`)

deve ser ativado o deploy automático.

### Alterar o workflow

No ficheiro:

```
.github/workflows/deploy.yml
```

substituir:

```yaml
on:
  workflow_dispatch:
```

por:

```yaml
on:
  push:
    branches:
      - main
```

---

## 🧠 O que acontece no deploy

Quando é feito push para a branch `main`:

1. O GitHub Actions constrói a imagem Docker da aplicação
2. A imagem é enviada para o GitHub Container Registry (GHCR)
3. O Helm atualiza a aplicação no cluster Kubernetes (K3s)
4. O Kubernetes faz rolling update dos pods
5. A nova versão fica disponível automaticamente

⚠️ Não é necessário fazer `git pull` na VM.
O deploy é feito através de imagens Docker.

---

## ⚠️ Notas importantes

* Garantir que o secret `ghcr-secret` existe no namespace `deisi`
* Verificar que a porta da aplicação coincide com o `targetPort`
* Ajustar o ingress conforme necessário

---

## 📌 Objetivo

Este template permite:

* reduzir erros na criação de novas apps
* garantir consistência entre serviços
* automatizar completamente o deploy

---

## 👨‍💻 Resultado

Com este template, qualquer nova aplicação pode ser colocada em produção no cluster com:

* poucas alterações
* sem configuração manual no servidor
* pipeline totalmente automatizada
