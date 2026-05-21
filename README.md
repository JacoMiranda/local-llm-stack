# 🤖 Local LLM Stack — vLLM + Open WebUI

Stack local para rodar modelos de linguagem com GPU NVIDIA usando **vLLM** como engine de inferência e **Open WebUI** como interface, com suporte a leitura de PDFs e documentos.

---

## 🖥️ Configuração de referência (máquina original)

| Componente | Especificação |
|---|---|
| **SO** | Ubuntu 24.04.4 LTS (Noble Numbat) |
| **CPU** | Intel Core Ultra 7 265 (20 cores) |
| **RAM** | 32 GB |
| **GPU** | NVIDIA GeForce RTX 5060 — **8 GB VRAM** |
| **Driver NVIDIA** | 595.58.03 |
| **Armazenamento** | NVMe 733 GB |

---

## 📦 Modelos disponíveis

| Serviço | Modelo | Porta | VRAM estimada | Contexto |
|---|---|---|---|---|
| `vllm-gemma` | google/gemma-2-2b-it | 8000 | ~5.5 GB | 6144 tokens |
| `vllm-llama` | meta-llama/Llama-3.2-3B-Instruct | 8001 | ~6.5 GB | 4096 tokens |
| `vllm-qwen` | Qwen/Qwen2.5-3B-Instruct | 8002 | ~6.5 GB | 8192 tokens |

> ⚠️ Com **8 GB de VRAM**, rode **apenas um modelo de cada vez**.

---

## 💡 Sugestões para outras configurações de hardware

### GPU com 6 GB de VRAM (ex: RTX 3060, RTX 4060)
- Use os mesmos modelos com `--gpu-memory-utilization 0.85`
- Reduza o contexto: `--max-model-len 2048`
- Alternativa: modelos menores como `google/gemma-2-2b-it` ou `Qwen/Qwen2.5-1.5B-Instruct`

### GPU com 12–16 GB de VRAM (ex: RTX 3080, RTX 4070 Ti, RTX 4080)
- Pode rodar modelos **7B** sem quantização:
  - `meta-llama/Llama-3.1-8B-Instruct`
  - `Qwen/Qwen2.5-7B-Instruct`
  - `mistralai/Mistral-7B-Instruct-v0.3`
- Aumente o contexto: `--max-model-len 16384`
- Com 16 GB pode rodar dois modelos pequenos simultaneamente

### GPU com 24 GB de VRAM (ex: RTX 3090, RTX 4090, RTX 5090)
- Modelos **13B–14B** sem quantização
- Modelos **70B** com quantização 4-bit (`--quantization bitsandbytes`)
- Pode rodar dois modelos 7B simultaneamente

### Sem GPU (apenas CPU)
- Substitua `vllm` por **Ollama** (mais otimizado para CPU)
- Use modelos quantizados Q4: `ollama pull qwen2.5:3b`
- Espere respostas mais lentas (~1–5 tokens/s)

---

## 🔧 Pré-requisitos

- Docker + Docker Compose
- NVIDIA GPU com driver ≥ 525 e [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html)
- Token do HuggingFace com acesso aos modelos:
  - [google/gemma-2-2b-it](https://huggingface.co/google/gemma-2-2b-it) — aceitar termos
  - [meta-llama/Llama-3.2-3B-Instruct](https://huggingface.co/meta-llama/Llama-3.2-3B-Instruct) — aceitar termos
  - [Qwen/Qwen2.5-3B-Instruct](https://huggingface.co/Qwen/Qwen2.5-3B-Instruct) — acesso público

---

## ⚙️ Configuração

```bash
# 1. Clone o repositório
git clone <url-do-seu-repositorio>
cd <nome-do-repositorio>

# 2. Configure o token do HuggingFace
cp .env.example .env
# Edite o .env e coloque seu token real
nano .env
```

---

## 🚀 Como usar

### Iniciar a interface + um modelo

```bash
# Gemma 2 2B (mais leve, bom para conversas gerais)
docker compose up -d vllm-gemma open-webui

# Llama 3.2 3B (bom para tarefas e instruções)
docker compose up -d vllm-llama open-webui

# Qwen 2.5 3B (excelente para PDFs e análise de documentos)
docker compose up -d vllm-qwen open-webui
```

### Alternar entre modelos

```bash
# Parar modelo atual
docker compose stop vllm-gemma

# Subir outro (libera a VRAM automaticamente)
docker compose up -d vllm-llama
```

> O Open WebUI detecta automaticamente quais modelos estão ativos — sem precisar reiniciar a interface.

### Acessar a interface

Abra **http://localhost:3000** no navegador.

Para usar PDFs: no chat, clique no ícone de **clipe** e envie o arquivo junto com sua pergunta.

### Ver logs em tempo real

```bash
docker compose logs -f vllm-gemma
```

### Parar tudo

```bash
docker compose down
```

---

## 📁 Estrutura dos arquivos

```
.
├── docker-compose.yml   # Definição dos serviços
├── .env.example         # Template de configuração (copie para .env)
├── .env                 # Suas credenciais (NÃO suba para o Git)
├── .gitignore
└── README.md
```

---

## 🔗 Recursos

- [vLLM Docs](https://docs.vllm.ai)
- [Open WebUI Docs](https://docs.openwebui.com)
- [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html)
- [HuggingFace Tokens](https://huggingface.co/settings/tokens)
