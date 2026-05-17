# CryptoBot Pro — 24/7 Server

Your bot runs on a server. Close the browser. Bot keeps trading.

## Files
- `server.js` — The trading bot (runs on server, never stops)
- `dashboard.html` — Your control panel (open in any browser, anywhere)
- `package.json` — Node.js config

---

## Quickest Deploy: Railway.app (Free, 5 minutes)

1. **Create GitHub repo** (free at github.com)
   - Upload `server.js` and `package.json` to the repo

2. **Go to railway.app** → Sign up free → New Project → Deploy from GitHub
   - Select your repo
   - Railway auto-detects Node.js and deploys

3. **Add environment variables** in Railway dashboard → Variables:
   ```
   BOT_PIN = 123456      ← change to your own 6-digit PIN
   PORT    = 3000
   ```

4. **Get your server URL** from Railway (looks like `https://xyz.up.railway.app`)

5. **Open dashboard.html** in your browser
   - Enter your Railway URL + PIN
   - Save config, enter API keys, click Start

6. **Done.** Close your browser. Bot keeps running on Railway.

---

## Alternative: VPS ($4/month, most reliable)

DigitalOcean / Vultr / Hetzner — all have $4–6/month servers.

```bash
# 1. SSH into your server
ssh root@YOUR_SERVER_IP

# 2. Install Node.js
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs

# 3. Upload files (from your computer)
scp server.js package.json root@YOUR_SERVER_IP:/root/cryptobot/

# 4. SSH back in and run
cd /root/cryptobot
npm install

# 5. Install PM2 (keeps bot running forever, auto-restarts on crash)
npm install -g pm2
BOT_PIN=yourpin pm2 start server.js --name cryptobot
pm2 save
pm2 startup   # makes it survive server reboots

# 6. Check it's running
pm2 status
pm2 logs cryptobot

# 7. Your bot URL
http://YOUR_SERVER_IP:3000
```

### Open port 3000 on VPS firewall:
```bash
sudo ufw allow 3000
sudo ufw enable
```

---

## API Endpoints (what dashboard uses)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /ping | Health check (no auth) |
| GET | /status | Full bot state |
| POST | /start | Start the bot |
| POST | /stop | Stop the bot |
| POST | /config | Save settings + API keys |
| POST | /reset | Reset stats |

All endpoints except `/ping` require header: `X-Bot-Pin: yourpin`

---

## Security Notes

- Use HTTPS in production (Railway/Render give you HTTPS free)
- On VPS, put Nginx in front and use Let's Encrypt for free SSL
- Never enable Withdrawal permission on your exchange API key
- Whitelist your server's IP in exchange API settings

---

## Bot State

The bot saves its full state to `bot_state.json` every 10 seconds.
If the server restarts (e.g. deployment), it resumes automatically.
Trades, profit, orders all persist.
