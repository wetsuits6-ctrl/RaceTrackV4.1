Multi-device server access fix:
# 1. Start server inside WSL
npm run dev

# 2. Get WSL IP
ip addr show eth0

# 3. Get Windows IP
ipconfig

# 4. Setup port forwarding + firewall (run PowerShell/CMD as Admin)
# Replace <WSL-IP> with your WSL IP from step 2, and <WIN-IP> with your Windows IP from step 3
netsh interface portproxy add v4tov4 listenport=3000 listenaddress=0.0.0.0 connectport=3000 connectaddress=<WSL-IP>
# Allow TCP port 3000 through Windows Firewall
netsh advfirewall firewall add rule name="WSL 3000 Inbound" dir=in action=allow protocol=TCP localport=3000
netsh advfirewall firewall add rule name="WSL 3000 Outbound" dir=out action=allow protocol=TCP localport=3000
# 5. On your phone, open:
# http://<WIN-IP>:3000
npm run dev/npm run not working fix:
Insert following command into WSL terminal:
chmod +x node_modules/.bin/cross-env
