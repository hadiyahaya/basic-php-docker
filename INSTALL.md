# Prerequisite Setup: WSL, Ubuntu, and Docker

Before working through the Docker Compose tutorial series in `Readme.md`, you need a Linux environment with Docker installed. This guide walks through setting that up on Windows using WSL (Windows Subsystem for Linux).

---

## Part 1: Install WSL

1. Open **PowerShell as Administrator**.
2. Run:
   ```powershell
   wsl --install
   ```
   This enables the required Windows features, installs the WSL2 kernel, and installs Ubuntu by default.
3. Restart your computer when prompted.

**Check your WSL version:**
```powershell
wsl --version
```
If WSL was already installed and you just need to make sure you're on version 2:
```powershell
wsl --set-default-version 2
```

---

## Part 2: Install Ubuntu

If `wsl --install` didn't already set up Ubuntu for you (e.g., WSL was pre-existing), install it explicitly:

1. Open PowerShell and run:
   ```powershell
   wsl --install -d Ubuntu
   ```
   Alternatively, install **Ubuntu** from the Microsoft Store.
2. Launch Ubuntu from the Start menu. On first launch, it will finish setting up and prompt you to create a UNIX username and password.
3. Update your packages:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

**Useful WSL commands:**
* `wsl -l -v` (List installed distros and their WSL version)
* `wsl --set-default Ubuntu` (Set Ubuntu as your default distro)
* `wsl --shutdown` (Fully restart the WSL subsystem, useful after config changes)

---

## Part 3: Install Docker

1. Download and install [Docker Desktop for Windows](https://www.docker.com/products/docker-desktop/).
2. During installation, ensure **"Use WSL 2 instead of Hyper-V"** is checked.
3. After installing, open Docker Desktop, go to **Settings → Resources → WSL Integration**, and enable integration for your Ubuntu distro.
4. Open your Ubuntu terminal and verify Docker is available:
   ```bash
   docker --version
   docker compose version
   ```

---

## Part 4: Verify Everything Works

From your Ubuntu terminal, run:
```bash
docker run hello-world
docker compose version
```

If both commands succeed, you're ready to move on.

---

## Part 5: Set Up Your Projects Folder

1. Open your **Ubuntu** terminal (from the Start menu, or by running `wsl` in PowerShell).
2. Create a folder to keep your projects in, and move into it:
   ```bash
   mkdir -p ~/projects
   cd ~/projects
   ```
3. Clone this repository into it:
   ```bash
   git clone <repository-url>
   ```
   If `git` isn't installed yet, install it first with:
   ```bash
   sudo apt update && sudo apt install -y git
   ```

You're now ready to start the tutorial series in `Readme.md`.
