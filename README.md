# ChainBox Notarb Bot Manager & Auto Pilot - User Guide

## Notarb Bot Manager:
### **Quick Setup (7 Steps)**

### 1. Extract Files
Place the Bot Manager files in the same directory as your notarb-launcher.jar. 
Extract all files to a directory on your server. 
```bash
unzip chainbox-notarb-manager.zip
```

### 2. Configure Global Settings**
```bash
nano notarb-global.toml
```

Inside the file, set your keypair path and RPC URL like below:
```toml
DEFAULT_KEYPAIR_PATH="/path/to/your/keypair.txt"
DEFAULT_RPC_URL="https://your.rpc.endpoint"
```

### 3. Create Telegram Bot
1. Search for @BotFather on Telegram (https://t.me/BotFather)
2. Click Start and send `/newbot` and follow instructions
3. Copy the bot token (Example Token: 1234567890:ABCDEFGHIJKLMNOPQRSTUVWXYZ)
4. **IMPORTANT:** Click your bot and send `/start` to activate it


### 4. Get Your User ID
1. Search for @userinfobot on Telegram (https://t.me/userinfobot)
2. Click Start and send `/start` to get your user ID number (Example ID: 1234567890)

### 5. Install Bot in your VPS
```bash
chmod +x install.sh start.sh
./install.sh
```

### 6. Configure Bot
Edit the `.env` file:
```bash
nano .env
```

Fill in your information:
```env
LICENSE_KEY=your_license_key_here
TELEGRAM_TOKEN=your_telegram_bot_token_here  
ALLOWED_USER_IDS=your_telegram_user_id_here
```

### 7. Start Bot
```bash
./start.sh
```

## **Commands**

- **Start bot:** `./start.sh`
- **View logs:** `screen -r chainbox_tg_notarb`
 **Check logs:** `cd logs && cat bot.log or tail -n 300 bot.log`
- **Stop bot:** `screen -S chainbox_tg_notarb -X quit`
- **Check status:** `screen -list`

### Working with Screen (Log Viewing)
When you use `screen -r chainbox_tg_notarb` to view logs:
- **To detach from screen (leave bot running):** Press `Ctrl+A` then `D`
- **To stop the bot completely:** Press `Ctrl+C` or use the stop command above
- **To scroll up/down in logs:** Press `Ctrl+A` then `[`, use arrow keys, press `Esc` to exit scroll mode

## **Troubleshooting**

### Bot doesn't respond
1. Make sure you sent `/start` to the Telegram bot
2. Check your user ID is correct in `.env`
3. Verify bot token is correct in `.env`
4. Check logs in your VPS

### Permission denied
```bash
chmod +x notarb_bot_runner install.sh start.sh
```

### Screen not found
```bash
# Install screen
sudo apt-get install screen  # Ubuntu/Debian
sudo yum install screen      # CentOS/RHEL
sudo dnf install screen      # Fedora
```

### License Issues
- **"LICENSE_KEY not configured"** → Add license key to `.env`
- **"License expired"** → Contact chainbox.dev to renew
- **"License is bound to another machine"** → Contact chainbox.dev

## **File Structure**
```
chainbox-notarb-manager/
├── notarb_bot_runner      # Main bot executable
├── install.sh          # Installation script  
├── start.sh           # Start script
├── .env.example       # Configuration template
└── README.md         # This file
```

## **Support**
For support, contact **chainbox.dev**


# 🤖 ChainBox Auto-Pilot Setup Guide for Notarb Bot

## Overview

This guide explains how to set up your Notarb bot to receive automated trading signals from the automation service. The automation service detects hype tokens in real-time and sends START/STOP signals to your bot via webhook.

> **⚠️ IMPORTANT:** This README file is for users who have an Auto-Pilot License Key. If you do not have an Auto-Pilot License Key, even if auto-pilot mode is activated, you will not receive any signals. For more detailed information about Auto-Pilot, you can get information from the ChainBox Discord server: [https://discord.gg/UEGGkPmDhA](https://discord.gg/UEGGkPmDhA)

## Architecture

```
CHAINBOX (Automation Service)
         ↓
         [Detects hype]
         ↓
         [Sends START/STOP signals via webhook]
         ↓
VPS      (Your NOTARB Bot on port 8888)
         ↓
         [Receives signal]
         ↓
         [Creates config and starts/stops bot automatically]
```

## Prerequisites

- ✅ NOTARB bot already installed and running
- ✅ VPS/Server with public IP address
- ✅ Root or sudo access to configure firewall
- ✅ Bot configured with at least one sender type (SPAM/JITO/FAST/ASTRALANE)

## Step-by-Step Setup

### 1. 🌐 Network Configuration (VPS Setup)

#### Find Your Public IP Address
First, you need to find your VPS's public IP address to provide it for configuration:

```bash
# Method 1: Using curl
curl ifconfig.me
# or
curl ipinfo.io/ip

# Method 2: Using wget
wget -qO- ifconfig.me

# Method 3: Check your VPS provider's dashboard
# Most VPS providers show the public IP in their control panel
```

**📋 Important:** Copy this IP address and provide it to ChainBox authorities. This is required for signal reception.

#### Configure Secure Firewall for Port 8888
Your bot needs to receive HTTP requests from the automation service on port 8888. For security, we only allow connections from our automation service IP range.

**For Ubuntu/Debian (UFW) - RECOMMENDED SECURE SETUP:**
```bash
# Step 1: Close port 8888 to everyone
sudo ufw deny 8888

# Step 2: Allow only ChainBox automation service IPs
sudo ufw allow from 208.77.244.0/24 to any port 8888

# Step 3: Enable firewall and verify
sudo ufw enable
sudo ufw status
```

**Expected UFW status output:**
```
To                         Action      From
--                         ------      ----
8888                       DENY        Anywhere
8888                       ALLOW       208.77.244.0/24
```

**⚠️ SECURITY BENEFITS:**
- ✅ Only ChainBox automation service can reach your bot
- ✅ Blocks all unauthorized access attempts  
- ✅ Prevents port scanning and attacks
- ✅ No security risk to your VPS

**Alternative (Less Secure):**
If you prefer to open port 8888 to everyone (NOT recommended):
```bash
sudo ufw allow 8888
sudo ufw reload
```

### 2. 🔧 Bot Configuration

#### Check Bot Status
Ensure your NOTARB bot is running:
```bash
screen -ls
# Should show: chainbox_tg_notarb session
```

If not running, start it:
```bash
./start.sh
```

#### Verify Webhook Server
After starting the bot, check logs to confirm webhook server started:
```bash
screen -r chainbox_tg_notarb
# Look for: "✅ Webhook server started on port 8888"
# Look for: "📡 Ready to receive signals at http://0.0.0.0:8888/webhook/signal"
```

Press `Ctrl+A` then `D` to detach from screen.

### 3. 📡 Configure Auto-Pilot in Telegram Bot

Open your NOTARB Telegram bot and configure Auto-Pilot:

1. **Access Auto-Pilot Menu:**
   - /start to your bot
   - Click "🤖 Auto-Pilot"

2. **Enable Auto-Pilot:**
   - Click "❌ Status: OFF" to toggle to "✅ Status: ON"

3. **Select Sender Type:**
   - Click "📡 Sender: -"
   - Choose from available options:
     - **SPAM** - Standard transactions        
     - **JITO** - Jito bundle transactions
     - **FAST** - Fast transactions
     - **ASTRALANE** - Astralane transactions
   - ⚠️ **Important:** Sender must be configured in "Config File Parameters" first

4. **Configure Fee Mode:**
   - Click "💰 Fee Selection"
   - Choose fee mode:
     - **Static** - Uses fixed fees from your configuration
     - **Dynamic** - Uses real-time fee data from signals
   - ⚠️ Important: With Dynamic Fee enabled, set Max Fee and Tip caps — use the maximum you’re willing to pay. Our fee estimator smart-filters short-lived spikes, but hype can still require higher fees. The bot will never exceed your caps.

## 🔧 Troubleshooting

**Problem:** Bot not receiving signals
1. Verify Auto-Pilot is enabled in Telegram bot
2. Check sender type is configured
3. Verify webhook server is running on port 8888

### Configuration Issues

**Problem:** "Sender not ready" when selecting sender type
1. Go to "📁 Config File Parameters" in your bot
2. Configure the sender type (SPAM/JITO/FAST/ASTRALANE)
3. Fill in all required parameters
4. Return to Auto-Pilot and select the sender

## 🔒 Security Notes

1. **Firewall:** Port 8888 is restricted to ChainBox automation service IPs only (208.77.244.0/24)
2. **Authentication:** All webhook requests are authenticated with a secure token
3. **IP Whitelist:** Only authorized ChainBox automation IPs can reach your bot
4. **No Public Access:** Your webhook endpoint is not accessible from the internet
5. **No Sensitive Data:** No private keys or sensitive data are transmitted
6. **Attack Prevention:** UFW rules prevent port scanning and unauthorized access

## 📞 Support

If you encounter issues:

1. **Check this guide** - Most issues are covered in troubleshooting
2. **Verify network setup** - Port 8888 must be accessible
3. **Log file:** tail -f /path/to/your/bot/logs/bot.log
4. **Verify configuration** - Auto-Pilot enabled, sender configured!
5. **Contact support** - Provide logs and error messages

---

**🎯 Once configured, your bot will automatically receive trading signals and manage bots without manual intervention!**


[Screenshot 2025-09-25 at 16 45 17](https://github.com/user-attachments/assets/6de710a3-2064-4313-879a-f08ebf82cf5c)
