# Security Policy

## Supported Versions

We release patches for security vulnerabilities for the following versions:

| Version | Supported          |
| ------- | ------------------ |
| current (main branch) | :white_check_mark: |
| older versions | :x: |

## Reporting a Vulnerability

We take the security of LXC AutoScale seriously. If you discover a security vulnerability, please follow these steps:

### How to Report

1. **Do NOT open a public issue** for security vulnerabilities
2. **Email the maintainer** at fabrizio.salmi@gmail.com with:
   - A description of the vulnerability
   - Steps to reproduce the issue
   - Potential impact of the vulnerability
   - Any suggested fixes (if you have them)

### What to Expect

- **Acknowledgment**: You will receive an acknowledgment within 48 hours
- **Updates**: We will keep you informed about the progress of fixing the vulnerability
- **Credit**: If you wish, we will credit you for the discovery when we release a fix

### Disclosure Policy

- Please allow us reasonable time to address the vulnerability before public disclosure
- We aim to release security patches as quickly as possible
- We will coordinate with you on the disclosure timeline

## Security Best Practices

When using LXC AutoScale:

1. **Protect your configuration files**: The YAML configuration may contain sensitive information
2. **Use SSH keys instead of passwords** when connecting to remote Proxmox hosts
3. **Restrict file permissions**: Ensure configuration files are only readable by the service user
4. **Keep the software updated**: Regularly update to the latest version
5. **Review logs regularly**: Monitor `/var/log/lxc_autoscale.log` for unusual activity
6. **Backup configurations**: Keep backups of your configuration in a secure location

## Security Features

LXC AutoScale includes these security considerations:

- Lock file mechanism to prevent concurrent execution
- Backup creation before making changes
- Rollback capability to restore previous configurations

Thank you for helping keep LXC AutoScale secure!
