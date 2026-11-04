# Assembly_Network-distributor-master

An experimental proxy DHCP server written in ARM assembly for Linux. This project was transferred from the `master` branch of [Assembly_Network-distributor](https://github.com/CreperAvatar/Assembly_Network-distributor).

The program listens on DHCP port `67` and creates DHCP replies for PXE clients. The reply provides a TFTP server address derived from the network interface's IP address and the boot file name `pxelinux.0`. The source code sets PXE/DHCP options, including the `PXEClient` vendor class.

> This is an experimental prototype. Carefully verify its behavior and configuration before using it on a network; incorrect DHCP replies may affect other clients on the network.

## Files

- `proxyDHCP.s` – proxy DHCP server source code in ARM assembly.
- `proxyDHCP` – compiled executable.

## Requirements

- 32-bit ARM Linux compatible with the system calls used in the source code.
- GNU Binutils (`as` and `ld`) if you want to build the program from source.
- A network interface named `eth0`.
- Access to UDP port `67`; running the server will generally require root privileges or the appropriate capability.
- A PXE client and an available TFTP server serving the boot file `pxelinux.0`.

## Build

From the project root, run:

```sh
as -o proxyDHCP.o proxyDHCP.s
ld -o proxyDHCP proxyDHCP.o
```

## Configuration and running

The source code has the network interface `eth0` and boot file name `pxelinux.0` hard-coded. If your environment differs, change these values in `proxyDHCP.s` and rebuild the program.

Run it with:

```sh
sudo ./proxyDHCP
```

Run it only in a test or dedicated network where you control the DHCP/PXE configuration. Ensure that the boot file is available over TFTP at the address announced to the client.

## Security warning

DHCP uses broadcast communication, and the project does not appear to implement authentication. Incorrect or competing DHCP services may disrupt network configuration. TFTP also provides neither encryption nor authentication. Use this prototype only on an isolated, trusted network, and do not serve sensitive files over TFTP.
