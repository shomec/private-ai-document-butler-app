# 🎩 Private AI Document Butler

A completely private, air-gapped document organizer that uses a local LLM to "read" your files and move them into organized folders based on their content.

Traditional organizers only look at filenames. The **Document Butler** analyzes the actual content of PDFs or text files using **Ollama** before deciding where they belong. No data ever leaves your computer.

---

## 🏗️ The Local Stack

- **Workflow Engine**: [n8n](https://n8n.io/) (Docker Container)
- **Brain**: [Ollama](https://ollama.com/) running `gemma3:1b` (Docker Container)
- **Storage**: Local Bind Mount (Shared folder between your computer and n8n)
- **Trigger**: n8n's Local File Trigger node

---

## 🚀 Quick Start

### 1. Prerequisites
- [Docker](https://docs.docker.com/get-docker/) installed on your machine.
- [Docker Compose](https://docs.docker.com/compose/install/) (usually included with Docker Desktop).

### 2. Spin up the Stack
Navigate to the project directory and run:

```bash
docker compose up -d
```

This command will:
- Start the `ollama` container.
- Start a temporary `init-ollama` container to pull the `gemma3:1b` model.
- Start the `n8n` container.

### 3. Setup the Folder Structure
If not already present, create the required folders:

```bash
mkdir -p my-local-files/inbox
mkdir -p my-local-files/organized/Invoice
mkdir -p my-local-files/organized/Personal
mkdir -p my-local-files/organized/Technical
mkdir -p my-local-files/organized/Other
```

### 4. Import the Workflow
1. Open your browser and go to: `http://localhost:5678`
2. If this is your first time, complete the n8n setup.
3. In the left sidebar, go to **Workflows**.
4. Click **Add Workflow** (top right) -> **Import from File...**.
5. Select the `workflow.json` file provided in this repository.
6. Click the **Execute Workflow** button or toggle the **Active** switch to start monitoring.

---

## 📂 How it Works

1. **Inbox**: Drop any PDF or Text file into the `my-local-files/inbox` folder.
2. **Detection**: n8n detects the new file and reads its content.
3. **Intelligence**: The content is sent to the local **Ollama** instance.
4. **Categorization**: The LLM (`gemma3:1b`) identifies if it's an **Invoice**, **Personal**, or **Technical** document.
5. **Organization**: n8n moves the file into the corresponding subfolder under `my-local-files/organized/`.

---

## 🛡️ Privacy
This application is designed for complete privacy. 
- **No Cloud APIs**: Everything runs locally.
- **Volume Mounts**: Files stay on your local disk; n8n only gets access to the specific folder you mount.
- **Air-Gapped Ready**: Once the Docker images are pulled, no internet connection is required.

---

## ⚙️ Configuration Details
The following environment variables are used in `docker-compose.yml`:
- `N8N_BLOCK_FS_WRITE_ACCESS=false`: Allows n8n to move/write files on your local system via the volume mount.
- Volume Mount: `./my-local-files:/files` maps your local folder to `/files` inside the container.
