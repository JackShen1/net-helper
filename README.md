<h1 align="center">NET Helper</h1>

## 📋 &nbsp;Setup DHCP Server

**DHCP** (Dynamic Host Configuration Protocol) - automatically provides IP addresses and other network settings (subnet mask, gateway, etc.) to computers and various devices on the network.

The client sends a broadcast packet `DHCPDISCOVER` in search of a server. In turn, the DHCP server considers the request and sends a `DHCPOFFER` with possible IP addresses. The client selects the address and sends `DHCPREQUEST` with the selected address. The server then sends a confirmation (`DHCPACK`).

**Initial conditions:**

&#9;**`eth0`** `IP: 192.168.1.9/24`

1) Install the DHCP server with command:

```sh
$ sudo apt-get install isc-dhcp-server
```

2) In the file `/etc/default/isc-dhcp-server` you need to specify your interface:

<p align="center">
  <img src="img/dhcp-inter.png" alt="DHCP interface" width="600">
</p>

3) Configure the config file. Open the config file `/etc/dhcp/dhcpd.conf`. By default, the file contains config examples. You can delete the example and insert the following config:

```sh
default-lease-time 600;
max-lease-time 7200;

option subnet-mask 255.255.255.0;
option broadcast-address 192.168.1.255;
option routers 192.168.1.1;
option domain-name-servers 192.168.1.1, 8.8.8.8;
option domain-name "mydomain.example";

subnet 192.168.1.0 netmask 255.255.255.0 {
range 192.168.1.50 192.168.1.100;
}
```

- `default-lease-time` - the time for which the IP address is issued;
- `max-lease-time` - the maximum time for which the IP address is issued;
- `option subnet-mask` - the subnet mask to which the IP address is issued;
- `option routers` - the address of the router, also a possible option when one of the computers gives the IP address to another computer, then one computer is a router for another;
- `subnet 192.168.1.0` - our subnet;
- `range 192.168.1.50 192.168.1.100` - range of IP-addresses that will be issued.

4) Check ufw status, it should be **inactive**:

```sh
$ sudo ufw status
```

Otherwise:

```sh
$ ufw allow 67/udp
$ ufw reload
$ ufw show
```

5) Start DHCP:

```sh
$ sudo dhcpd
```

If you see the following message: `Can't open /var/lib/dhcp/dhcpd.leases for append`

Then modify the rights to this file using the following command:

```sh
$ sudo chmod 777 /var/lib/dhcp/dhcpd.leases
```

And run DHCP again:

```sh
$ sudo dhcpd
```

Now the DHCP server has to listen for requests on our interface. There must be lines `Listening on ...`, `Sending on ...`.

Alternatively, to start the DHCP service, you can use the command:

```sh
$ sudo systemctl start isc-dhcp-server.service
```

6) Now let's check if our server has started:

```sh
$ sudo systemctl status isc-dhcp-server.service
```

If the server is inactive, restart it with the command and check status again:

```sh
$ sudo systemctl restart isc-dhcp-server.service
$ sudo systemctl status isc-dhcp-server.service
```

If you want a service to start on every reboot then you need:

```sh
$ sudo systemctl enable isc-dhcp-server.service
```

7) To release the current address and get a new one, execute the following commands:

```sh
$ sudo dhclient -r eth0
$ sudo dhclient -v eth0
```


8) Enter the following command to make sure you get a new address:

```sh
$ ifconfig
```

or 

```sh
$ ip a
```


Now everything is ready! 

In Wireshark, you can view in detail the interaction of the DHCP server with the client. 
Also, a new entry will appear in the `var/lib/dhcp/dhcpd.leases` file.

## 📫 &nbsp;Get in touch

<p align="center">
<a href="https://www.linkedin.com/in/yevhenii-shendrikov-6795291b8/"><img src="https://img.shields.io/badge/-Jack%20Shendrikov-0077B5?style=flat&logo=Linkedin&logoColor=white"/></a>
<a href="mailto:jackshendrikov@gmail.com"><img src="https://img.shields.io/badge/-Jack%20Shendrikov-D14836?style=flat&logo=Gmail&logoColor=white"/></a>
<a href="https://www.facebook.com/jack.shendrikov"><img src="https://img.shields.io/badge/-Jack%20Shendrikov-1877F2?style=flat&logo=Facebook&logoColor=white"/></a>
<a href=""><img src="https://img.shields.io/badge/-@jackshen-0088cc?style=flat&logo=Telegram&logoColor=white"/></a>
</p>
