# Netrum Node Setup on Ubuntu: A Comprehensive Guide

This guide provides a detailed, step-by-step tutorial for installing and running a Netrum node on a fresh Ubuntu installation, including the installation of all necessary dependencies, hardware requirements, CLI command explanations and performing social tasks.

---

## Hardware & Network Requirements

To run the Netrum Node smoothly, make sure your system meets the following minimum requirements:

### Hardware Requirements

| Component   | Minimum    | Recommended   |
|-------------|------------|---------------|
| **CPU** | 2 Cores    | 2+ Cores      |
| **RAM** | 4 GB       | 6 GB or more  |
| **Disk Space**| 50 GB SSD  | 100 GB SSD    |

*SSD storage is highly recommended for faster performance and node stability.*

### Network Requirements

| Type       | Minimum Speed |
|------------|---------------|
| **Download** | 10 Mbps       |
| **Upload** | 10 Mbps       |

*A stable and fast internet connection is important for uptime sync, mining tasks, and daily reward claims.*

### Other requirements
You should have a Base name domain on your node wallet. If you don't have one, buy here: https://www.base.org/names

---

## 1. Prerequisites: System Update and Essential Tools

First, we need to update your system's package list and install the `build-essential` package, which includes the GCC compiler, `make`, and other tools necessary for compiling software from source.
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install build-essential -y
```

## 2. Install Go (Golang)
The Netrum node is written in Go. We will install the latest version of Go.

### a. Download and Extract Go
```bash
wget https://golang.org/dl/go1.22.5.linux-amd64.tar.gz
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go1.22.5.linux-amd64.tar.gz
rm go1.22.5.linux-amd64.tar.gz
```

### b. Set Go Environment Variables
Next, we need to add the Go binary to your system's PATH.
```bash
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.profile
echo 'export GOPATH=$HOME/go' >> ~/.profile
echo 'export PATH=$PATH:$GOPATH/bin' >> ~/.profile
source ~/.profile
```

### c. Verify Go Installation
To confirm that Go has been installed correctly, run the following command:
```bash
go version
```

## 3. Install Git
We'll use Git to clone the Netrum node repository
```bash
sudo apt install git -y
```
Verify the installation:
```bash
git --version
```

## 4. Build the Netrum Node
Now that all the dependencies are installed, we can clone the repository and build the node.

### a. Clone the Repository
```bash
git clone https://github.com/NetrumLabs/netrum-lite-node.git
cd netrum-lite-node
```

### b. Build the Node
Use the go build command to compile the source code.
```bash
go build .
```

This will create an executable file named `netrum-lite-node` in the current directory. To use the CLI commands easily, you can rename it and move it to your local bin directory.
```bash
mv netrum-lite-node netrum
sudo mv netrum /usr/local/bin/
```
Now you can run the `netrum` commands from anywhere in your terminal.

---

## 5. How to Use Netrum CLI Commands

Here is a list of available commands and their functions.

-   `netrum-system` : This command checks your VPS system status, including CPU, RAM, and internet speed. Use it to make sure your machine meets the basic requirements before setup.
* Wallet: you have 2 options for the wallet, create a new wallet, import it into your Metamask and mint a base domain:
-   `netrum-new-wallet` : Creates a new EVM-compatible wallet directly from the CLI. It will generate your public address and private key, all of which you should store securely.

or

Import your wallet that minted the base domain with it before (submit private key without 0x)


-   `netrum-import-wallet` : Allows you to import an existing wallet by entering your private key. This is useful if you already have a Base domain tied to a wallet you control.
-   `netrum-check-basename` : Checks whether your current wallet has a registered Base domain name. Your Base name will also become your Netrum Node ID.
-   `netrum-node-id` : Displays the current Node ID associated with your wallet. This is your official node identity on the Netrum network.
-   `netrum-node-sign` : Generates a signed identity message using your wallet's private key. This signature verifies that you own the node and wallet.
-   `netrum-node-register` : Registers your node on-chain and with the backend system. This step requires a small amount of BASE for gas (typically between 0.0002 and 0.0005 BASE).
-   `netrum-sync` : Starts the syncing process between your node and the Netrum network. This keeps your node active and eligible to earn mining rewards.
-   `netrum-sync-log` : Displays real-time logs showing your sync status, heartbeat signals, and activity tracking. Use this to confirm your node is working correctly.
-   `netrum-mining` : Begins the mining process for NPT tokens. Your node will now start earning rewards based on uptime and active sync.
-   `netrum-mining-log` : Shows live mining logs and confirms whether your node is earning tokens correctly. This is useful for monitoring daily activity.
-   `netrum-claim` : Lets you claim your mined NPT tokens after 24 hours of uptime. Once claimed, mining will automatically restart. This step requires a small amount of BASE for gas (around 0.00002 to 0.00003 BASE).
-   `netrum-wallet` : Displays wallet information including your public address and NPT balance. Use this to check if your mined rewards have been received.
-   `netrum-wallet-key` : Reveals both the public and private keys of your wallet. Only use this in a secure environment and never share your private key.
-   `netrum-wallet-remove` : Deletes your wallet from the local VPS. Make sure to back up your private key or seed phrase before running this command, as this action cannot be undone.

## Update
If the team announce an update on their Discord, you just need to go to the node directory
```bash
cd netrum-lite-node
```
and send pull request
```bash
git pull
```
Then check logs
```bash
netrum-mining-log
```
---


## 6. (Optional) Run Node as a Systemd Service
To ensure your node runs continuously in the background and restarts automatically (for commands like `netrum-sync` or `netrum-mining`), you can create a `systemd` service.

### a. Create the Service File
```bash
sudo nano /etc/systemd/system/netrumd.service
```

### b. Add the Following Content
Paste the following into the editor. **Make sure to replace `your_user` with your actual username and `your_command` with the command you want to run (e.g., `netrum-sync`).**
```ini
[Unit]
Description=Netrum Lite Node Service
After=network.target

[Service]
User=your_user
Type=simple
ExecStart=/usr/local/bin/netrum your_command
Restart=on-failure
RestartSec=5
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
```
Save and close the file. (In `nano`, press `Ctrl+X`, then `Y`, then `Enter`).

### c. Enable and Start the Service
```bash
sudo systemctl daemon-reload
sudo systemctl enable netrumd
sudo systemctl start netrumd
```

### d. Check the Service Status
You can check the status of your node at any time with:
```bash
sudo systemctl status netrumd
```
To view the logs in real-time:
```bash
sudo journalctl -u netrumd -f
```
---

Now join Discord: https://discord.gg/eWZATZGKZs
Go to `#bot-commands` channel and send this command to register your node
```bash
/register <your-node-id>
```
You can do zealy tasks: https://zealy.io/cw/netrumai/invite/zcyCM-0bBlmeSqBDsR-BC

And also Guild points: https://guild.xyz/netrum-labs

For the testnet, you should get this NFT and get its role on the Guild: https://base.netrumlabs.com

To submit your holding NPT amount, go to `#bot-commands` channel on Discord and send `/mytoken` command

---
Give a star and join our [Telegram Channel](https://t.me/irdropper) for support
