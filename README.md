# Run [Eliza](https://github.com/ai16z/eliza) on a Digital Ocean Droplet

Warning: These provisioning scripts are in an early alpha, works-for-me state. They need peer review before safely using in production.

## Quick Start

Create a new Digital Ocean droplet (tested on the cheapest dedicated CPU they do).

**Make sure to add an ssh key to the droplet via the UI and choose the LTS version of Ubuntu (24.04).**

When the droplet is ready, you can ssh into it as root.

```bash
# become root (this is the only time you'll be doing this)
ssh root@your-droplet-ip -i ~/.ssh/your-private-key

# clone this repo
git clone https://github.com/bealers/eliza-remote.git
cd eliza-remote && chmod +x install.sh

# run the install script (this will take a while)
# edit this first to set your own admin username
./install.sh
```

## Post-Installation

**Open a NEW terminal**
```bash
ssh shrike@your-droplet-ip -i ~/.ssh/your-private-key
```

If that works you can close the root terminal session, you will always log in as the maintenance user from now on.

From your new terminal session, you can now configure Eliza.

**Configure .env**
```bash
sudo vi /opt/eliza/.env
```
At minimum you probably want to set `OPENAI_API_KEY` and `ANTHROPIC_API_KEY`

**(Re)start service**
```bash
sudo systemctl restart eliza
```

**Test**
```bash
sudo eliza-cli
```

## Configuration & Logs

### Files
- `.env`: Main configuration
- `characters/default.character.json`: Active character
- `/var/log/eliza/`: Log directory
  - `eliza.log`: Application logs
  - `eliza-error.log`: Error logs
  - `install.log`: Installation logs (can be deleted)

### Service Control
```bash
sudo systemctl {start|stop|restart|status} eliza
```

### View Logs in realtime
```bash
sudo journalctl -u eliza -f
```

## Maintenance

### Updates (Untested)
```bash
sudo -i -u eliza
cd /opt/eliza
git pull
git checkout $(git describe --tags --abbrev=0)
pnpm install
exit
sudo systemctl restart eliza
```

### Character Switching (UNTESTED)
```bash
sudo -i -u eliza
cd /opt/eliza
ln -sf characters/trump.character.json characters/default.character.json
exit
sudo systemctl restart eliza
```

## Current Status

### Working
- User setup (maintenance and service user)
- All app dependencies (NVM, Node, pnpm)
- Eliza build with workspace support
- Basic security hardening
- Log rotation and management
- CLI interface for testing
- HTTP API (port 3000, firewalled by default)

### TODO
- [ ] Test character switching via symlinks
- [ ] SSL support
- [ ] Multiple characters support

## Interfaces

Eliza provides two ways to interact:

### 1. API Service (Port 3000)
- Runs as a system service
- RESTful API endpoints
- Suitable for web interfaces/integrations
- Managed via systemd
- External access blocked by default

### 2. Test CLI Interface (Port 3001)
- Run with: `eliza-cli`
- Note: This will temporarily stop the API service
- Type 'exit' to quit and restart the service
- may delete

## Contributing & Feedback

Feedback welcome! If people find this useful I have 1000 things I could do to improve it.

PRs particularly welcome around opsec.

## License

MIT License

## Credit
H/T to [HowieDuhzi](https://github.com/HowieDuhzit) for the WSL install script that inspired this fork.
