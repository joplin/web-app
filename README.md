# Joplin Web App

This repository deploys the Joplin web client to https://app.joplincloud.com/ with GitHub pages.

The web app source code can be found in [the main Joplin repository](https://github.com/laurent22/joplin).

## Running Joplin WebApp with Docker

**Prerequisites:**

* Docker installed and running.
* A running instance of Joplin Server, accessible via a URL (e.g., `https://joplin-server.yourdomain.com`).

**1. Pull the Docker Image:**

Fetch the latest official Joplin WebApp image:

```bash
docker pull ghcr.io/adamoutler/web-app:latest
```

**2. Run the Docker Container:**

Run the container, mapping a host port (e.g., `8088`) to the container's port `80`, and giving it a recognizable name. Running in detached mode (`-d`) is recommended for background operation.

```bash
docker run -d --name joplin-webapp -p 8088:80 ghcr.io/adamoutler/web-app:latest
```

* You will initially access the WebApp via `http://<your-docker-host-ip>:8088`.
* **Note:** The WebApp container itself doesn't handle HTTPS. This is typically managed by a reverse proxy (see Step 3).

**3. Configure HTTPS and Reverse Proxy:**

Both the Joplin WebApp and Joplin Server **must** be served over HTTPS for secure communication and browser compatibility. A reverse proxy (like Nginx, Apache, Caddy, or Traefik) is the standard way to achieve this.

* Set up a reverse proxy for the Joplin WebApp container (listening on port `8088` locally) to serve it under your desired HTTPS domain (e.g., `https://joplin-webapp.yourdomain.com`).
* You can obtain free SSL/TLS certificates from [Let's Encrypt](https://letsencrypt.org/).

**4. Configure CORS Headers on Joplin Server:**

Because the WebApp (e.g., `https://joplin-webapp.yourdomain.com`) and the Joplin Server (e.g., `https://joplin-server.yourdomain.com`) are running on different origins (domains/ports), you must configure Cross-Origin Resource Sharing (CORS) headers on the **Joplin Server's reverse proxy**. This tells the browser that it's safe for the WebApp to make requests to the Server API.

**Nginx Example:**

Add the following within the `server` or `location` block handling your Joplin Server traffic in your Nginx configuration.

```nginx
# Set this variable to the exact origin (scheme + domain + port)
# from which your Joplin WebApp is served.
set $my_origin 'https://joplin-webapp.yourdomain.com; # <-- CHANGE THIS
if ($request_method = 'OPTIONS') {
    add_header 'Access-Control-Allow-Origin'  $my_origin always; 
    add_header 'Access-Control-Allow-Credentials' 'true' always; 
    add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, DELETE, PATCH, OPTIONS' always;
    add_header 'Access-Control-Allow-Headers' 'Authorization, Content-Type, Accept, Origin, X-Requested-With, X-Api-Key, X-Api-Auth, DNT, Cache-Control, If-Modified-Since, If-None-Match, Range, x-api-min-version' always; 
    add_header 'Access-Control-Max-Age' 86400 always;
    add_header 'Content-Length' 0;
    return 204;
}

proxy_hide_header Access-Control-Allow-Origin;
proxy_hide_header Access-Control-Allow-Credentials;
add_header Access-Control-Allow-Origin  $my_origin  always;
add_header Access-Control-Allow-Credentials 'true' always;
```

* **Crucial:** Replace `https://joplin-webapp.yourdomain.com` with the *actual URL* you use to access the Joplin WebApp in your browser.
* **Apache/Other Proxies:** Equivalent directives exist for other servers (e.g., `Header set Access-Control-Allow-Origin "..."` in Apache). Consult your reverse proxy's documentation for CORS configuration.

**5. (Potential) Configure Joplin Server URL in WebApp:**

The WebApp needs to know the URL of your Joplin Server API. This is often configured via environment variables passed to the Docker container. Check the Joplin WebApp's documentation for the specific environment variable name (it might be something like `JOPLIN_API_BASE_URL` or similar).

Example if the variable was `JOPLIN_API_BASE_URL`:

```bash
docker run -d --name joplin-webapp \
-p 8088:80 \
-e JOPLIN_API_BASE_URL='[https://joplin-server.yourdomain.com](https://joplin-server.yourdomain.com)' \
ghcr.io/joplin/web-app:latest
```

* Replace `https://joplin-server.yourdomain.com` with the public HTTPS URL of your Joplin Server.
* **Note:** Consult the official Joplin WebApp documentation or image source for the correct environment variable name if needed.

**6. Access the Application:**

Once configured, you should be able to access the Joplin WebApp via the HTTPS URL you set up in your reverse proxy (e.g., `https://joplin-webapp.yourdomain.com`). It will then communicate with your Joplin Server in the background.


## FAQ

### What is it?

Joplin Web is Joplin Mobile, running in a web browser.

### Where is my data stored?

Like Joplin Mobile, Joplin Web is local-first. Notes and attachments are stored locally on your computer, but can optionally be synced with one of the supported sync targets. As a result, Joplin Web can be used offline.

### What browsers does it support?

The Joplin web app works best in recent versions of Chrome and Safari. It can also be used in Firefox, however, it may take a very long time to start.

Some features are available only on certain platforms:
- File system sync[^1] (as of July 2024):
	- ✅ Chrome (desktop)
	- ❌ Chrome (Android)
	- ❌ Safari
	- ❌ Firefox
- Share note content[^2] (as of July 2024):
	- ✅ Safari
	- ✅ Chrome (Android)
	- ❌ Chrome (Desktop)
	- ❌ Firefox
- Insert images from a camera (as of July 2024):
	- ✅ Safari
	- ✅ Chrome (Android)
	- ❌ Chrome (Desktop)
	- ❌ Firefox
- Drop images and files from another app (as of July 2024):
	- ✅ Chrome (Desktop), Safari, Firefox (Desktop)
	- ❌ Chrome (Android)


[^1]: Requires [support for showDirectoryPicker](https://caniuse.com/?search=showDirectoryPicker).
[^2]: Requires [Web Share API support](https://caniuse.com/?search=web%20share%20api).


