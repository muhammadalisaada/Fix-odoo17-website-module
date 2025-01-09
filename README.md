**How to Fix the Odoo 17 Website Editor Issue When Using SSL with Nginx**

I encountered an issue with Odoo 17 after installing the Website module on my server, which is running inside an LXC container with Nginx set up as a reverse proxy. The problem manifested in the inability to edit the website or load pages in the editor, where it would continuously show "Loading" without anything happening.

### Error Details:
The error appeared as follows:

```
UncaughtPromiseError > OwlError

Uncaught Promise > An error occurred in the owl lifecycle (see this Error's "cause" property)
...
```

Another error appeared when attempting to add a new page:

```
UncaughtPromiseError > TypeError
Uncaught Promise > Failed to fetch
...
```

### Causes:
After investigating, I found that the main reason for the issue was that Odoo's web editor needs to access the website using the HTTP protocol to create an iframe. However, when SSL/TLS encryption is enabled (as with Nginx), the server blocks the insecure HTTP protocol, leading to the error.

### Solution:
The issue was resolved by adding the following line to the Odoo configuration file (`/etc/odoo/odoo.conf`):

```
proxy_mode=True
```

After adding this line, I restarted both Odoo and Nginx services, and the issue was fixed. I was then able to edit the website through my domain using HTTPS.

### Alternative Solutions:
I haven’t tried the following solutions, but they might be useful:
1. Try accessing directly via IP using the HTTP protocol.
2. Add the following parameters in the Odoo system settings:
   ```
   web.base.url=your domain
   web.base.url.freeze=true
   ```
   Also, ensure to include `proxy_mode` and `workers` in the `odoo.conf` file.
