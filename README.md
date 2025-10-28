# Nginx Configuration: Get Real Client IP Behind Cloudflare Tunnel

This repository contains an essential Nginx configuration snippet for web servers running behind **Cloudflare Tunnel** (`cloudflared`).

## 🚨 The Problem
When you use Cloudflare Tunnel, the `cloudflared` service on your server forwards all requests to Nginx (or your other web server). As a result, Nginx sees all connections as originating from `127.0.0.1` (localhost).

This causes issues with:
* Nginx log files (all visitors appear to have the same IP).
* Web applications (PHP, Node, etc.) trying to get the visitor's IP via `$remote_addr`.
* IP-based security rules or rate limiting.

## ⚙️ The Solution
The solution is to use Nginx's `ngx_http_realip_module` to read a custom header sent by Cloudflare. When using Cloudflare Tunnel (or the Cloudflare proxy), the original visitor's IP is sent in the `CF-Connecting-IP` header.

The configuration below tells Nginx to:
1.  Trust requests coming from `127.0.0.1` (as this is your `cloudflared` tunnel).
2.  Trust all official Cloudflare IP ranges (just in case you also use the orange-cloud proxy).
3.  Take the real IP from the `CF-Connecting-IP` header and use it to populate the `$remote_addr` variable.

---

## Nginx Configuration

Copy the code below and save it as a new file inside your Nginx configuration directory, for example:
`/etc/nginx/cloudflare_ips.conf`

```nginx
set_real_ip_from 127.0.0.1;
set_real_ip_from ::1;

# Cloudflare IP Ranges
set_real_ip_from 173.245.48.0/20;
set_real_ip_from 103.21.244.0/22;
set_real_ip_from 103.22.200.0/22;
set_real_ip_from 103.31.4.0/22;
set_real_ip_from 141.101.64.0/18;
set_real_ip_from 108.162.192.0/18;
set_real_ip_from 190.93.240.0/20;
set_real_ip_from 188.114.96.0/20;
set_real_ip_from 197.234.240.0/22;
set_real_ip_from 198.41.128.0/17;
set_real_ip_from 162.158.0.0/15;
set_real_ip_from 104.16.0.0/13;
set_real_ip_from 104.24.0.0/14;
set_real_ip_from 172.64.0.0/13;
set_real_ip_from 131.0.72.0/22;

# IPv6 Cloudflare Ranges
set_real_ip_from 2400:cb00::/32;
set_real_ip_from 2606:4700::/32;
set_real_ip_from 2803:f800::/32;
set_real_ip_from 2405:b500::/32;
set_real_ip_from 2405:8100::/32;
set_real_ip_from 2c0f:f248::/32;
set_real_ip_from 2a06:98c0::/29;
