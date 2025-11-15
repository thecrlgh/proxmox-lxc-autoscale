# 🚀 LXC AutoScale

**LXC AutoScale** is a resource management daemon specifically designed for Proxmox environments. It automatically adjusts CPU and memory allocations with no downtime and can clone LXC containers based on real-time usage metrics and predefined thresholds. Can be run locally or remotely to make your containers always optimized for performance, managing spikes in demand, and optionally preserving resources during off-peak hours. 

- **✅ Works with `Proxmox 8.3.3`** 

**Quick Start**

| Method           | Instructions                                                                                                   |
|------------------|----------------------------------------------------------------------------------------------------------------|
| 🐳    | [Docker](https://github.com/fabriziosalmi/proxmox-lxc-autoscale/blob/main/docs/lxc_autoscale/README.md#docker) |
| 🐧    | [no Docker](https://github.com/fabriziosalmi/proxmox-lxc-autoscale/blob/main/README.md#quick-start) |

## Features
LXC AutoScale is packed with features that make it an essential tool for managing the auto-scaling of your LXC containers on Proxmox:

- ⚙️ Automatic Resource Scaling
- ⚖️ Automatic Horizontal Scaling
- 📊 Tier Defined Thresholds
- 🛡️ Host Resource Reservation
- 🔒 Ignore Scaling Option
- 🌱 Energy Efficiency Mode
- 🚦 Container Prioritization
- 📦 Automatic Backups
- 🔔 Mail and Push Notifications
- 📈 JSON Metrics
- 💻 Local or remote execution
- 💃 Easy autoconf for humans
- 🐳 Docker supported

> [!NOTE]
> If You need to autoscale Virtual Machines resources on Proxmox hosts You will like [this project](https://github.com/fabriziosalmi/proxmox-vm-autoscale).

## Quick Start

### Prerequisites

Before installing LXC AutoScale, ensure you have:

- **Proxmox VE 8.x** (tested with 8.3.3) running on your host
- **Python 3.6+** installed on the system
- **Root or sudo access** to the Proxmox host
- **LXC containers** already created and configured
- **Internet connection** for downloading the installation script

> [!IMPORTANT]
> LXC AutoScale requires LXCFS to be properly configured. See the LXCFS configuration section below for details.

### Installation

Getting started with LXC AutoScale on your Proxmox host is quick and simple:

```bash
curl -sSL https://raw.githubusercontent.com/fabriziosalmi/proxmox-lxc-autoscale/main/install.sh | bash
```

> [!TIP]
> Once installed, the service should be up and running. You can verify this by executing:
>
> ```bash
> systemctl status lxc_autoscale.service
> ```

If the conditions set in the configuration are met, you will quickly observe scaling operations in action.

### LXCFS Configuration (Important)

> [!IMPORTANT]
> You need to check your `/lib/systemd/system/lxcfs.service` file for the presence of the `-l` option which makes `loadavg` retrieval work as expected. Here is the required configuration:
>
> ```
> [Unit]
> Description=FUSE filesystem for LXC
> ConditionVirtualization=!container
> Before=lxc.service
> Documentation=man:lxcfs(1)
> 
> [Service]
> OOMScoreAdjust=-1000
> ExecStartPre=/bin/mkdir -p /var/lib/lxcfs
> # ExecStart=/usr/bin/lxcfs /var/lib/lxcfs
> ExecStart=/usr/bin/lxcfs /var/lib/lxcfs -l
> KillMode=process
> Restart=on-failure
> ExecStopPost=-/bin/fusermount -u /var/lib/lxcfs
> Delegate=yes
> ExecReload=/bin/kill -USR1 $MAINPID
>
> [Install]
> WantedBy=multi-user.target
> ```
> 
> Just update the `/lib/systemd/system/lxcfs.service` file, execute `systemctl daemon-reload && systemctl restart lxcfs`, and when you are ready to apply the fix, restart the LXC containers.
> 
> _Thanks to No-Pen9082 for pointing this out. [Here](https://forum.proxmox.com/threads/lxc-containers-shows-hosts-load-average.45724/page-2) is the Proxmox forum thread on the topic._

## Configuration

LXC AutoScale is designed to be highly customizable. You can reconfigure the service at any time to better suit your specific needs. For detailed instructions on how to adjust the settings, please refer to the **[official documentation](https://github.com/fabriziosalmi/proxmox-lxc-autoscale/blob/main/docs/lxc_autoscale/README.md)**.

> [!TIP]
> If you need LXC AutoScale configuration for all your LXC containers, you can automatically generate it by running this command:
> ```bash
> curl -sSL https://raw.githubusercontent.com/fabriziosalmi/proxmox-lxc-autoscale/main/lxc_autoscale/lxc_autoscale_autoconf.sh | bash
> ```

### Additional Resources

LXC AutoScale can be used and extended in many ways. Here are some useful additional resources:

- 🌐 [LXC AutoScale UI - Simple web UI to check scaling actions and logs](https://github.com/fabriziosalmi/proxmox-lxc-autoscale/tree/main/lxc_autoscale/ui)
- 🎛️ [LXC AutoScale - TIER snippets for 40 self-hosted apps](https://github.com/fabriziosalmi/proxmox-lxc-autoscale/blob/main/docs/lxc_autoscale/examples/README.md)

> [!TIP]
> LXC AutoScale ML has been moved to a separate [repository](https://github.com/fabriziosalmi/proxmox-lxc-autoscale-ml).

## Frequently Asked Questions

### Can I use this on Proxmox 7.x?

LXC AutoScale is tested on Proxmox VE 8.3.3. While it may work on older versions, compatibility is not guaranteed. We recommend using Proxmox VE 8.x for the best experience.

### Will this work with my existing containers?

Yes! LXC AutoScale works with existing LXC containers. Just configure the container IDs in the YAML file, and the service will start managing them.

### Does this support virtual machines (VMs)?

No, LXC AutoScale is specifically designed for LXC containers. For VM autoscaling, check out [proxmox-vm-autoscale](https://github.com/fabriziosalmi/proxmox-vm-autoscale).

### Can I run this remotely?

Yes! You can run LXC AutoScale on a separate machine and connect to your Proxmox host via SSH. Set `use_remote_proxmox: true` in the configuration and provide SSH credentials.

### Is it safe to use in production?

LXC AutoScale includes safety features like backups before changes and rollback capabilities. However, we recommend thoroughly testing it in a non-production environment first to understand how it behaves with your workload.

### How often does it check container resources?

The default polling interval is 300 seconds (5 minutes), but you can adjust this with the `poll_interval` setting in the configuration.

### Can I exclude certain containers from autoscaling?

Yes, add container IDs to the `ignore_lxc` list in the configuration file, and they will be excluded from autoscaling.

For more detailed questions and answers, see the [Q&A documentation](https://github.com/fabriziosalmi/proxmox-lxc-autoscale/blob/main/docs/q%26a/README.md).

## Contributing

LXC AutoScale is an open-source project, and contributions are welcome! Whether you want to submit a pull request, report an issue, or suggest a new feature, your input is invaluable. To get involved, you can:

- [Open an issue](https://github.com/fabriziosalmi/proxmox-lxc-autoscale/issues/new/choose) to report bugs or request new features.
- Submit a pull request to the repository.
- Fork the repository to experiment and develop your custom features.

## Other Projects

If you like this project, you may also like these:

- [caddy-waf](https://github.com/fabriziosalmi/caddy-waf) Caddy WAF (Regex Rules, IP and DNS filtering, Rate Limiting, GeoIP, Tor, Anomaly Detection) 
- [patterns](https://github.com/fabriziosalmi/patterns) Automated OWASP CRS and Bad Bot Detection for Nginx, Apache, Traefik and HaProxy
- [blacklists](https://github.com/fabriziosalmi/blacklists) Hourly updated domains blacklist 🚫 
- [proxmox-vm-autoscale](https://github.com/fabriziosalmi/proxmox-vm-autoscale) Automatically scale virtual machines resources on Proxmox hosts 
- [UglyFeed](https://github.com/fabriziosalmi/UglyFeed) Retrieve, aggregate, filter, evaluate, rewrite and serve RSS feeds using Large Language Models for fun, research and learning purposes 
- [DevGPT](https://github.com/fabriziosalmi/DevGPT) Code together, right now! GPT powered code assistant to build project in minutes
- [websites-monitor](https://github.com/fabriziosalmi/websites-monitor) Websites monitoring via GitHub Actions (expiration, security, performances, privacy, SEO)
- [caddy-mib](https://github.com/fabriziosalmi/caddy-mib) Track and ban client IPs generating repetitive errors on Caddy 
- [zonecontrol](https://github.com/fabriziosalmi/zonecontrol) Cloudflare Zones Settings Automation using GitHub Actions 
- [lws](https://github.com/fabriziosalmi/lws) linux (containers) web services
- [cf-box](https://github.com/fabriziosalmi/cf-box) cf-box is a set of Python tools to play with API and multiple Cloudflare accounts.
- [limits](https://github.com/fabriziosalmi/limits) Automated rate limits implementation for web servers 
- [dnscontrol-actions](https://github.com/fabriziosalmi/dnscontrol-actions) Automate DNS updates and rollbacks across multiple providers using DNSControl and GitHub Actions 
- [proxmox-lxc-autoscale-ml](https://github.com/fabriziosalmi/proxmox-lxc-autoscale-ml) Automatically scale the LXC containers resources on Proxmox hosts with AI
- [csv-anonymizer](https://github.com/fabriziosalmi/csv-anonymizer) CSV fuzzer/anonymizer
- [iamnotacoder](https://github.com/fabriziosalmi/iamnotacoder) AI code generation and improvement


## ⚠️ Disclaimer
> [!CAUTION]
> The author assumes no responsibility for any damage or issues that may arise from using this tool.

## License

LXC AutoScale is licensed under the MIT License, which means you are free to use, modify, and distribute this software with proper attribution. For more details, please see the [LICENSE](LICENSE) file.
