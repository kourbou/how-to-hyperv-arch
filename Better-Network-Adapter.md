# Set up an Internal Switch with Network Address Translation (NAT)

## 1. Creating and configuring the switch

- Open a PowerShell console as Administrator and create the internal switch.

    ```powershell
    New-VMSwitch -SwitchName "NAT Switch" -SwitchType Internal
    ```

- Find the interface index of the virtual switch you just created.
    ```powershell
    Get-NetAdapter -Name "vEthernet (NAT Switch)"
    ```

    Your output should be similar to below. In this case the interface index is 70.

    ```console
    PS C:\> Get-NetAdapter -Name "vEthernet (NAT Switch)"

    Name                      InterfaceDescription                    ifIndex Status       MacAddress             LinkSpeed
    ----                      --------------------                    ------- ------       ----------             ---------
    vEthernet (NAT Switch)    Hyper-V Virtual Ethernet Adapter #4          70 Up           00-15-5D-32-63-15        10 Gbps

    ```

- Configure the switch's subnet using [New-NetIPAddress](https://docs.microsoft.com/powershell/module/nettcpip/New-NetIPAddress).

    ```powershell
    New-NetIPAddress -IPAddress <NAT Gateway IP> -PrefixLength <NAT Subnet Prefix Length> -InterfaceIndex <ifIndex>
    ```

    For example, you can configure the 172.20.0.0/24 subnet with 256 addresses like so:

    ```powershell
    New-NetIPAddress -IPAddress 172.20.0.1 -PrefixLength 24 -InterfaceIndex 70
    ```

- Configure Network Address Translation using [New-NetNat](https://docs.microsoft.com/en-us/powershell/module/netnat/New-NetNat).

    ```powershell
    New-NetNat -Name <NATOutsideName> -InternalIPInterfaceAddressPrefix <NAT subnet prefix>
    ```

    Using the same 172.20.0.0/24 subnet you would use:

    ```powershell
    New-NetNat -Name "NAT Switch" -InternalIPInterfaceAddressPrefix 172.20.0.0/24
    ```

- You can now select the switch when configuring the virtual machine. This allows you to assign a static IP in the
  subnet from the guest O/S. You will also need to specify the gateway IP configured above.

## 2. Connecting an Arch Linux VM to the virtual switch

- Instead of enabling `dhcpcd.service` you can use `systemd-networkd`.

```console
# systemctl enable --now systemd-networkd.service
```

- Create a `.network` file for the interface:

<pre><code># nano /etc/systemd/network/10-static.network
...
<b>[Match]
Name=en*
Name=eth*

[Network]
Address=172.20.0.2
Gateway=172.20.0.1

# Google Public DNS
DNS=8.8.8.8
DNS=8.8.4.4
</b></code></pre>

- Reload the network configuration and check status:

```console
# networkctl reload
# networkctl status eth0
```

- Enable and start `systemd-resolved.service` for DNS lookups.

```console
# systemctl enable --now systemd-resolved.service
```

- Map `/etc/resolv.conf` to the stub file provided by `systemd-resolved.service`.

```console
# ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf
```