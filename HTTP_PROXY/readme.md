Here's the **README.md** formatted version of the documentation:


# Nginx HTTP and HTTPS Proxy Server with SSL/TLS

This guide will show you how to set up an **HTTP** and **HTTPS** proxy server using **Nginx**. It covers basic HTTP proxy setup and an HTTPS proxy setup with SSL/TLS encryption for secure connections.

---

## Prerequisites

1. **Nginx Installed**:
   Ensure that Nginx is installed on your server. If not, install it with the following commands:

   ```bash
   sudo apt update
   sudo apt install nginx
   ```

2. **SSL Certificate**:
   You will need an SSL certificate for the HTTPS proxy setup. You can use **Let's Encrypt** for a free SSL certificate or generate a **self-signed certificate** for testing.

   - [Let's Encrypt Setup Guide](https://certbot.eff.org/)
   - Alternatively, you can create a **self-signed certificate** for testing purposes.

3. **DNS Resolution**:
   Ensure that the domain names you intend to proxy can be resolved by the Nginx server. If necessary, configure a DNS resolver.

---

## Step 1: Basic HTTP Proxy Setup

This section walks you through setting up an HTTP proxy server that listens on port `8080`.

### 1.1. Configure Nginx for HTTP Proxy

1. Open or create a new configuration file for your HTTP proxy server, e.g., `/etc/nginx/conf.d/proxy.conf`:

   ```bash
   sudo nano /etc/nginx/conf.d/proxy.conf
   ```

2. Add the following HTTP proxy configuration:

   ```nginx
   # HTTP Proxy Server Configuration
   server {
       listen 8080;  # Listen on port 8080 for HTTP proxy requests

       # Handle HTTP Proxying
       location / {
           proxy_pass http://$host$request_uri;  # Proxy to the requested URL
           proxy_set_header Host $host;  # Preserve the original host header
           proxy_set_header X-Real-IP $remote_addr;  # Pass the real client IP
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;  # Add X-Forwarded-For header
           proxy_set_header X-Forwarded-Proto $scheme;  # Preserve the protocol (http/https)

           # Optionally, you can enable caching
           proxy_cache_bypass $http_cache_control;
       }
   }
   ```

### 1.2. Define DNS Resolver (Optional)

To enable domain name resolution, open `/etc/nginx/nginx.conf` and add a DNS resolver:

```bash
sudo nano /etc/nginx/nginx.conf
```

Add the following inside the `http` block:

```nginx
http {
    # Define DNS resolver
    resolver 8.8.8.8 8.8.4.4 valid=300s;  # Google's public DNS servers

    # Other configurations
    include /etc/nginx/conf.d/*.conf;  # Include all config files from /conf.d
}
```

### 1.3. Test Nginx Configuration

Run a configuration test to check for syntax errors:

```bash
sudo nginx -t
```

If successful, reload Nginx to apply changes:

```bash
sudo systemctl reload nginx
```

---

## Step 2: HTTP Proxy with SSL (HTTPS Proxy)

This section covers setting up an HTTPS proxy server with SSL encryption.

### 2.1. Obtain SSL Certificate

You can either use **Let's Encrypt** for a free SSL certificate or create a **self-signed SSL certificate** for testing.

#### Using Let's Encrypt:

1. Install Certbot and the Nginx plugin:

   ```bash
   sudo apt install certbot python3-certbot-nginx
   ```

2. Obtain the SSL certificate:

   ```bash
   sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
   ```

   Certbot will automatically configure SSL for your domain.

#### Using a Self-Signed Certificate (for testing):

1. Generate a self-signed SSL certificate:

   ```bash
   sudo openssl req -x509 -newkey rsa:4096 -keyout /etc/nginx/ssl/private.key -out /etc/nginx/ssl/certificate.crt -days 365
   ```

2. When prompted, enter the details such as country, state, and common name (use your domain name for the common name).

### 2.2. Configure HTTPS Proxy Server

1. Open or create a configuration file for your HTTPS proxy server:

   ```bash
   sudo nano /etc/nginx/conf.d/proxy_https.conf
   ```

2. Add the following configuration for the HTTPS proxy server:

   ```nginx
   # HTTPS Proxy Server Configuration
   server {
       listen 443 ssl;  # Listen on port 443 for HTTPS proxy requests
       server_name yourdomain.com;  # Replace with your domain name

       ssl_certificate /etc/nginx/ssl/certificate.crt;  # Path to your SSL certificate
       ssl_certificate_key /etc/nginx/ssl/private.key;  # Path to your private key

       ssl_protocols TLSv1.2 TLSv1.3;  # Enable only secure TLS protocols
       ssl_ciphers 'TLS_AES_128_GCM_SHA256:TLS_AES_256_GCM_SHA384:ECDHE-RSA-AES128-GCM-SHA256';  # Strong ciphers

       # Handle HTTPS Proxying
       location / {
           proxy_pass https://$host$request_uri;  # Proxy to the requested URL over HTTPS
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto $scheme;

           # Optionally, enable caching
           proxy_cache_bypass $http_cache_control;
       }
   }

   # HTTP to HTTPS redirection (Optional)
   server {
       listen 80;  # Listen on HTTP port 80 for HTTP requests
       server_name yourdomain.com;  # Replace with your domain name

       # Redirect HTTP requests to HTTPS
       return 301 https://$host$request_uri;
   }
   ```

### 2.3. Test Nginx Configuration

After making changes, test the Nginx configuration:

```bash
sudo nginx -t
```

If successful, reload Nginx to apply the changes:

```bash
sudo systemctl reload nginx
```

---

## Step 3: Configure Firewall (Allow Ports 80 and 443)

If you're using a firewall (e.g., UFW), ensure that ports `80` (HTTP) and `443` (HTTPS) are open:

```bash
sudo ufw allow 80,443/tcp
```

---

## Step 4: Verify Proxy Server

1. Open a browser and test your **HTTP proxy** by accessing `http://yourdomain.com:8080`.
2. Test your **HTTPS proxy** by accessing `https://yourdomain.com`.

Ensure that your SSL certificate is valid and that traffic is being correctly proxied to the backend server.

---

## Troubleshooting

- **DNS Resolution Errors**: If you see DNS resolution errors like `no resolver defined`, ensure that you've added a `resolver` directive in the `nginx.conf` file.
- **SSL Errors**: If you encounter SSL-related issues, check your SSL certificate paths and verify the certificate is correctly installed.

---

## Conclusion

This guide shows you how to set up both an **HTTP** and **HTTPS** proxy server using **Nginx**. It includes steps for acquiring an SSL certificate using **Let's Encrypt** or creating a **self-signed certificate**. With these configurations, your proxy server will handle both non-secure and secure (SSL-encrypted) traffic.

---

Happy proxying! 🚀
```

---

This `README.md` provides a clear step-by-step guide for setting up both **HTTP** and **HTTPS** proxy servers with **SSL/TLS encryption** using **Nginx**. Feel free to customize the domain names and certificate paths as per your setup.
