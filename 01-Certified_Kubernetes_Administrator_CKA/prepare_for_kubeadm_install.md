# Install Containerd

Install containerd from the package here: https://github.com/containerd/containerd/releases/latest and extract it under /usr/local

```
# download the release binary
wget https://github.com/containerd/containerd/releases/download/v2.2.0/containerd-2.2.0-linux-amd64.tar.gz

# extract the binaries into /usr/local/bin, placing executables like containerd, ctr, containerd-shim-runc-v2, and containerd-stress in that location
sudo tar Cxzvf /usr/local containerd-2.2.0-linux-amd64.tar.gz

# create a symbolic link from /usr/local/bin/containerd to /usr/local/sbin/containerd
# This symlink ensures that containerd can be found in /usr/local/sbin, which is where some systemd service files or init systems might expect to find the containerd daemon binary.
sudo ln -sf /usr/local/bin/containerd /usr/local/sbin/containerd

```

> 
> Configure systemd units for containerd:

```
cat <<'EOF_UNIT' | sudo tee /etc/systemd/system/containerd.service
[Unit]
Description=containerd container runtime
Documentation=https://containerd.io
After=network.target

[Service]
ExecStart=/usr/local/bin/containerd
Restart=always
RestartSec=5
RuntimeDirectory=containerd
Delegate=yes
KillMode=process
OOMScoreAdjust=-999

[Install]
WantedBy=multi-user.target
EOF_UNIT
```

```
sudo systemctl daemon-reload
sudo systemctl enable --now containerd
```

Verify the service:

```
sudo systemctl status containerd --no-pager
```

> Set System Parameters
```
Set sysctl -w net.ipv4.ip_forward equal to 1

Set net.bridge.bridge-nf-call-iptables equal to 1
```

```
modprobe br_netfilter
echo 1 > /proc/sys/net/bridge/bridge-nf-call-iptables
echo 1 > /proc/sys/net/ipv4/ip_forward
```

```
In order to persist after system reboot, you need to edit /etc/sysctl.conf and set:

net.ipv4.ip_forward=1
net.bridge.bridge-nf-call-iptables=1

I’m also seeing docs that reference these two parameters as well.

net.ipv4.conf.all.forwarding=1
net.bridge.bridge-nf-call-ip6tables=1

After editing the file, you can apply the changes immediately with sudo sysctl -p
```
