# Lab-01: Exploração Simples de Path Traversal (File Retrieval)

🎯 **Objetivo do Laboratório**

Explorar uma vulnerabilidade de *Path Traversal* no parâmetro `filename` do endpoint `/image` para recuperar o conteúdo do arquivo `/etc/passwd` do sistema operacional do servidor (Linux).

---

## 1. Exploração Manual (Burp Suite)

O ataque foi inicialmente realizado através do **Burp Suite** para inspecionar a resposta do servidor.

**Passos realizados**

* **Captura da Requisição:** O tráfego foi interceptado no Burp Proxy durante a tentativa de carregar uma imagem (ex.: `/image?filename=x.jpg`).
* **Modificação no Repeater:** A requisição interceptada foi enviada ao Burp Repeater para modificação manual.
* **Injeção do Payload:** O valor do parâmetro `filename` foi alterado para um payload de *Path Traversal*.

Exemplo de requisição enviada:

```http
GET /image?filename=../../../../etc/passwd HTTP/2
```

**Confirmação da vulnerabilidade**

* O servidor respondeu com **HTTP 200 OK** e o corpo da resposta continha o conteúdo do arquivo `/etc/passwd`, provando o sucesso do ataque.

---

## 2. Exploração Automatizada (Script Python)

**Fonte do Script:** Este script foi adaptado de um vídeo explicativo sobre Path Traversal: [Link do Vídeo](https://www.youtube.com/watch?v=40R8gYfAoAY)

O script chamado `directory-traversal-lab-01.py` foi utilizado para automatizar o envio da requisição e a verificação da resposta, demonstrando como criar um Proof-of-Concept (Prova de Conceito).

### Requisitos

* Dependências instaladas via `pip install -r ../requirements.txt`.

* O **Burp Suite** deve estar aberto e escutando em `127.0.0.1:8080` (conforme configuração presente no script), caso o script esteja configurado para usar proxy.

### Comando de execução

> O script utiliza a URL do laboratório como argumento.

```bash
python directory-traversal-lab-01.py https://[SEU_LAB_ID].web-security-academy.net/
```

### Saída de sucesso (exemplo)

O script verifica a presença da string `root:x` no corpo da resposta e confirma o sucesso:

```
(+) Exploiting the directory traversal vulnerability...
(+) Exploit successful!
(+) The following is the content of the /etc/passwd file:
root:x:0:0:root:/root:/bin/bash
...
peter:x:12001:12001::/home/peter:/bin/bash
carlos:x:12002:12002::/home/carlos:/bin/bash
...
```

---

## 3. Detalhes Técnicos e Payload

### Payload de sucesso

| Parâmetro  | Valor injetado           |
| ---------- | ------------------------ |
| `filename` | `../../../../etc/passwd` |

### Análise de erro de conexão comum

Durante a execução do script, pode ocorrer o erro:

```
ConnectionRefusedError: [WinError 10061]
```

**Causa:**
O script estava configurado para rotear o tráfego para o proxy local (`127.0.0.1:8080`), mas o Burp Suite não estava ativo ou não estava escutando nesta porta no momento da execução.

**Solução:**
Garantir que o **Burp Suite** esteja iniciado e que o **Proxy Listener** esteja ativo em `127.0.0.1:8080` antes de executar o script.

---
