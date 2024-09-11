# HAProxy Setup and Configuration

# HAProxy Setup and Configuration

HAProxy, or High Availability Proxy, is an open-source software that provides high availability, load balancing, and proxying for TCP and HTTP-based applications. It ensures high performance, reliability, and uninterrupted service delivery.

## HAProxy Configuration

The following is an example of a basic HAProxy configuration:

```bash
frontend http_front
    bind *:443 ssl crt /etc/haproxy/certs/nextgenapi.u11.com.pem crt /etc/haproxy/certs/nextgenetback.u11.com.pem crt /etc/haproxy/certs/nextgenredis.u11.com.pem crt /etc/haproxy/certs/nextgentrm.u11.com.pem
    bind *:80
    mode http
    redirect scheme https if !{ ssl_fc }
    acl trm_cloudware hdr(host) -i nextgentrm.u11.com
    acl etbackk_cloudware hdr(host) -i nextgenetback.u11.com
    acl laravel_cloudware hdr(host) -i nextgenapi.u11.com
    acl redis_cloudware hdr(host) -i nextgenredis.u11.com
    acl trm_dev_cloudware hdr(host) -i nextgendevtrm.u11.com
    use_backend trm_cloudware_backend if trm_cloudware
    use_backend etbackk_cloudware_backend if etbackk_cloudware
    use_backend laravel_cloudware_backend if laravel_cloudware
    use_backend redis_cloudware_backend if redis_cloudware
    use_backend trm_dev_cloudware_backend if trm_dev_cloudware
backend trm_cloudware_backend
    mode http
    balance roundrobin
    server trm_instance1 10.0.0.10:80 check
backend etbackk_cloudware_backend
    mode http
    balance roundrobin
    server etbackk_instance1 10.0.0.21:80 check
backend laravel_cloudware_backend
    mode http
    balance roundrobin
    server laravel_instance1 10.0.0.10:80 check
backend redis_cloudware_backend
    mode http
    balance roundrobin
    server redis_instance1 10.0.0.14:6379 check
backend trm_dev_cloudware_backend
    mode http
    balance roundrobin
    server trm_instance1 10.0.0.11:80 check
```

## Adding a New Subdomain and Forwarding Traffic to a New Server

To add a new subdomain and forward traffic to a new server, follow these steps:

1. **Stop the HAProxy service:**
    
    ```bash
    systemctl stop haproxy.service
    ```
    
2. **Add the backend configuration:**
    
    ```bash
    backend new_subdomain_backend
        mode http
        balance roundrobin
        server new_instance1 10.0.0.12:80 check
    ```
    
3. **Add the frontend configuration:**
    
    ```bash
    acl new_subdomain hdr(host) -i newsubdomain.u11.com
    use_backend new_subdomain_backend if new_subdomain
    ```
    
4. **Generate the SSL certificate for the new subdomain:**
    
    ```bash
    certbot certonly --standalone -d newsubdomain.u11.com
    # The SSL certificates are stored in /etc/letsencrypt/live
    ```
    
    Combine the SSL certificate and key into a single file:
    
    ```bash
    cat /etc/letsencrypt/live/newsubdomain.u11.com/fullchain.pem /etc/letsencrypt/live/newsubdomain.u11.com/privkey.pem > /etc/haproxy/certs/newsubdomain.u11.com.pem
    ```
    
5. **Update the frontend to include the new SSL certificate:**
    
    ```bash
    frontend http_front
        bind *:443 ssl crt /etc/haproxy/certs/nextgenapi.u11.com.pem crt /etc/haproxy/certs/nextgenetback.u11.com.pem crt /etc/haproxy/certs/nextgenredis.u11.com.pem crt /etc/haproxy/certs/nextgentrm.u11.com.pem crt /etc/haproxy/certs/newsubdomain.u11.com.pem
    ```
    
6. **Start the HAProxy service:**
    
    ```bash
    systemctl start haproxy.service
    ```
    

By following these steps, you can efficiently add a new subdomain to HAProxy and ensure that traffic is correctly forwarded to the new server. This setup helps maintain high availability and reliability for your applications.