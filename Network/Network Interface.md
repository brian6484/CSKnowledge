## Network Interface
- Connection point between ur PC/server and network
- kinda like the doorway where ur machine sends and receives data on a network

### Types of NI
- Physical: ehternet card(etho0, en0), wifi card(wlan0)
- Virtual: loopback(lo), Docker bridge(docker0), VPN interface (tun0)

## Properties
- Every NI has one more IP address assigned so that it can communicate

## Where is it used and can be found
```
ifconfig    # macOS/Linux
ip a        # Linux (modern)
```
u will see a list of interfaces like
1) lo: loopback (used for talking to urself 127.0.0.1)
2) etho0/ens33: wired network card
3) wlan0: wifi card

so when we do
```
sudo tcpdump -i eth0
```
the -i eth0 is telling tcpdump to listen on this specific eth0 interface. If u choose the **wrong interface, u wont see
packets that u expect**.
