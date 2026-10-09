# MXS Dashboard Setup Guide



---



 Prerequisites



 Install tmux (Ubuntu/WSL)



```bash
sudo apt update
sudo apt install tmux
```



Verify the installation:



```bash
tmux -V
```



---



## Create the Script



Create a scripts directory:



```bash
mkdir -p ~/scripts
```



Create the script:



```bash
nano ~/scripts/start-mxs-dashboard.sh
```



Paste the following script into the file:



```bash
#!/bin/bash



# ==========================================
# Configuration
# Update this path to match your local setup
# ==========================================
REPO_ROOT="$HOME/Alstom"



SESSION="mxs-dashboard"



# Kill existing session if it exists
tmux kill-session -t "$SESSION" 2>/dev/null



# Create session in repository root
tmux new-session -d -s "$SESSION" -c "$REPO_ROOT"



# Start all services in pane 0
tmux send-keys -t "$SESSION":0.0 "mxs start all" C-m



# Create 8 more panes
for i in {1..8}; do
    tmux split-window -t "$SESSION":0
    tmux select-layout -t "$SESSION":0 tiled
done



# Logs
tmux send-keys -t "$SESSION":0.1 "sleep 10 && mxs logs ebitoolmx-cbss" C-m
tmux send-keys -t "$SESSION":0.2 "sleep 10 && mxs logs ebitoolmx-ceviz" C-m
tmux send-keys -t "$SESSION":0.3 "sleep 10 && mxs logs ebitoolmx-frontend" C-m
tmux send-keys -t "$SESSION":0.4 "sleep 10 && mxs logs ebitoolmx-ils" C-m
tmux send-keys -t "$SESSION":0.5 "sleep 10 && mxs logs mx-acl-service" C-m
tmux send-keys -t "$SESSION":0.6 "sleep 10 && mxs logs mx-frontend-be" C-m
tmux send-keys -t "$SESSION":0.7 "sleep 10 && mxs logs mx-product-service" C-m



# Frontend terminal
tmux send-keys -t "$SESSION":0.8 "cd $REPO_ROOT/ebitoolmx-frontend" C-m



# Ceviz terminal
tmux split-window -h -t "$SESSION":0.8 -p 50
tmux send-keys -t "$SESSION":0.9 "cd $REPO_ROOT/ebitoolmx-ceviz" C-m



# Focus frontend pane
tmux select-pane -t "$SESSION":0.8



# Attach
tmux attach-session -t "$SESSION"
```



Save and exit:



```text
Ctrl + O
Enter
Ctrl + X
```



---



## Configure Your Repository Location



The script uses a configurable repository root:



```bash
REPO_ROOT="$HOME/Alstom"
```



Update this value to match your local setup.



Examples:



```bash
REPO_ROOT="$HOME/Alstom"
```



```bash
REPO_ROOT="$HOME/Projects"
```



```bash
REPO_ROOT="$HOME/workspace"
```



```bash
REPO_ROOT="/mnt/c/Users/<username>/dev"
```



All repositories are expected to exist under the specified root directory.



Example:



```text
~/Alstom
├── ebitoolmx-cbss
├── ebitoolmx-ceviz
├── ebitoolmx-frontend
├── ebitoolmx-ils
├── mx-acl-service
├── mx-frontend-be
└── mx-product-service
```



---



## Make the Script Executable



```bash
chmod +x ~/scripts/start-mxs-dashboard.sh
```



Verify:



```bash
ls -l ~/scripts/start-mxs-dashboard.sh
```



Expected output:



```text
-rwxr-xr-x
```



---



## Test the Script



Run:



```bash
~/scripts/start-mxs-dashboard.sh
```



If everything is configured correctly, tmux should:



1. Create a new session
2. Start all services
3. Open log panes
4. Open frontend and ceviz terminals
5. Attach automatically



---



## Create a Shortcut Command



Instead of running:



```bash
~/scripts/start-mxs-dashboard.sh
```



every time, create a shortcut command.



### Open your Bash configuration



```bash
nano ~/.bashrc
```



Add the following line to the bottom of the file:



```bash
alias mxsdash='~/scripts/start-mxs-dashboard.sh'
```



Save and exit:



```text
Ctrl + O
Enter
Ctrl + X
```



Reload your Bash configuration:



```bash
source ~/.bashrc
```



Start the dashboard with:



```bash
mxsdash
```



---



## Verify the Alias



```bash
alias mxsdash
```



Expected output:



```bash
alias mxsdash='~/scripts/start-mxs-dashboard.sh'
```



---



## Useful tmux Commands



### tmux Prefix Key



Most tmux commands start with:



```text
Ctrl + B
```



Press `Ctrl + B`, release both keys, then press the command key.



---



### Move Between Panes



```text
Ctrl + B + ↑
Ctrl + B + ↓
Ctrl + B + ←
Ctrl + B + →
```



Example:



```text
Ctrl + B + →
```



Moves to the pane on the right.



---



### Switch Directly to a Pane



Show pane numbers:



```text
Ctrl + B q
```



Then press the pane number you want.



---



### Scroll Log Output



Enter scroll mode:



```text
Ctrl + B [
```



While in scroll mode you can:



- Use Arrow Keys
- Use Page Up / Page Down
- Use Home / End



Exit scroll mode:



```text
q
```



---



### Enable Mouse Support (Recommended)



Create or edit your tmux configuration:



```bash
nano ~/.tmux.conf
```



Add:



```text
set -g mouse on
```



Reload tmux:



```bash
tmux source-file ~/.tmux.conf
```



Or restart tmux.



This enables:



- Mouse scrolling
- Pane selection via mouse
- Pane resizing via mouse



---



### Detach Without Stopping Anything



```text
Ctrl + B d
```



All services and logs continue running in the background.



---



### Reattach Later



```bash
tmux attach -t mxs-dashboard
```



---



### List Active Sessions



```bash
tmux ls
```



Example:



```text
mxs-dashboard: 1 windows
```



---



### Stop the Dashboard



```bash
tmux kill-session -t mxs-dashboard
```



---



## Quick Reference



- Move between panes → `Ctrl + B` + Arrow Key
- Show pane numbers → `Ctrl + B q`
- Scroll logs → `Ctrl + B [`
- Exit scroll mode → `q`
- Detach session → `Ctrl + B d`
- List sessions → `tmux ls`
- Reattach session → `tmux attach -t mxs-dashboard`
- Stop session → `tmux kill-session -t mxs-dashboard`



---



## Daily Usage



Once everything is configured:



```bash
mxsdash
```



This will:



- Start all MXS services
- Open log panes for key services
- Open dedicated frontend and ceviz terminals
- Attach you directly to the dashboard session
