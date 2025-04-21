![Cloud Skeleton](./assets/logo.jpg)

[![GPLv3 License](https://img.shields.io/badge/License-GPLv3-blue.svg)](LICENSE)
[![SSL Security A+](https://img.shields.io/badge/SSL_Security-A+-green)](https://www.ssllabs.com/ssltest/)
[![Security.txt ✓](https://img.shields.io/badge/Security.txt-%E2%9C%93-yellow)]()
[![Max_RAM-56M](https://img.shields.io/badge/Max_RAM-56M-violet)]()

# **[Cloud Skeleton](https://github.com/cloud-skeleton/)** ► **[Container Proxy](https://github.com/cloud-skeleton/container-proxy/)** 🚀

> This repository contains the configuration and deployment files for the **[Cloud Skeleton](https://github.com/cloud-skeleton/)** **[Container Proxy](https://github.com/cloud-skeleton/container-proxy/)**. It leverages **[Traefik](https://doc.traefik.io/traefik/)** as a secure reverse proxy to manage and route traffic in cloud-native environments, and includes a lightweight server that serves a signed `security.txt` file along with a public **[GPG](https://www.gnupg.org/gph/en/manual.html)** key (`info@cloudskeleton.eu.public.asc`) used for signature verification. This server is the **[Static Web Server](https://static-web-server.net/configuration/config-file/)**.

## Overview

The **[Container Proxy](https://github.com/cloud-skeleton/container-proxy/)** project provides a robust reverse proxy setup using **[Traefik](https://doc.traefik.io/traefik/)** to:
- Manage incoming HTTP/HTTPS traffic.
- Securely route requests to backend services.
- Automate TLS certificate management via **[Let's Encrypt](https://letsencrypt.org/getting-started/)**.
- Serve a regularly updated `security.txt` file for transparency and secure communication.
- Deliver an A+ SSL configuration as verified by **[SSL Labs](https://www.ssllabs.com/ssltest/)**.

![SSL Labs Rating A+](./assets/ssllabs-rating.jpg)

> **IMPORTANT:** Before deploying the **[Container Proxy](https://github.com/cloud-skeleton/container-proxy/)**, **you must complete all the prerequisites detailed in the [Prerequisites](https://github.com/cloud-skeleton/prerequisites) repository.** This step is essential to ensure that your system meets all the required configurations, dependencies, and security measures necessary for a successful deployment.

The primary configuration is defined in the `compose.yml` file, which sets up two main services:
- **[Traefik](https://doc.traefik.io/traefik/):** The reverse proxy service.
- **[Static Web Server](https://static-web-server.net/configuration/config-file/):** A server that serves the `security.txt` file at `/.well-known/security.txt`.

## Security File Rotation & Verification

- **Monthly Rotation:**  
  The `security.txt` file is automatically rotated every month. It is updated with new expiration dates and other dynamic information. To ensure the system always serves the latest version, it is recommended to automate a `git pull` followed by a `docker compose restart` (or a similar solution) on a monthly basis.

- **Signature Verification:**  
  The `security.txt` file is **[GPG](https://www.gnupg.org/gph/en/manual.html)**-signed to guarantee its authenticity. Alongside this file, the repository provides the public **[GPG](https://www.gnupg.org/gph/en/manual.html)** key (`info@cloudskeleton.eu.public.asc`). Use this key to verify the signature of `security.txt` and ensure that it has not been tampered with.

Example `security.txt` content:
```
-----BEGIN PGP SIGNED MESSAGE-----
Hash: SHA512

Contact: https://github.com/orgs/cloud-skeleton/discussions
Expires: 2025-04-01T00:00:00.000Z
Encryption: https://raw.githubusercontent.com/cloud-skeleton/container-proxy/refs/heads/main/security/info@cloudskeleton.eu.public.asc
Preferred-Languages: en, lt
-----BEGIN PGP SIGNATURE-----

iMAFARYKAEAiIQUfdwoiua8kFjVUqCf2L9D8pQvjFaujEN1WfTmAxzESAQUCZ8W/
eBYcaW5mb0BjbG91ZHNrZWxldG9uLmV1AAB79QHHW9jcVX6oowFyHtd0nTWwlWTP
l+uVv41XQu2LoFtcsXAnYHXRuktilKsUZO2Ip9lbYQtlDyjly4OAAciZl9Z6WaPb
sl3NfHNvoczSlCNcMGWc4W9DHI6SO9wcaVteb8oDC/4bznLv/USzdVZgOSWSTAkg
BQA=
=/b6R
-----END PGP SIGNATURE-----
```

## Configuration

The deployment is driven by a compose file that utilizes several environment variables. You can define these variables in a `.env` file located alongside `compose.yml`.

### Environment Variables

- **ADMIN_ALLOW_IP_CIDR**  
  *Description:* The IP CIDR allowed to access the **[Traefik](https://doc.traefik.io/traefik/)** dashboard.  
  *Example:*  
  ```env
  ADMIN_ALLOW_IP_CIDR=203.0.113.42/32
  ```

- **CERTIFICATE_EMAIL**  
  *Description:* The email address used for **[Let's Encrypt](https://letsencrypt.org/getting-started/)** certificate registration.  
  *Example:*  
  ```env
  CERTIFICATE_EMAIL=admin@example.com
  ```

- **DOCKER_SOCKET**  
  *Description:* The path to the **[Docker](https://docs.docker.com/get-started/)** socket. Useful if running on a host with a non-standard socket path.  
  *Default:* `/var/run/docker.sock`  
  *Example:*  
  ```env
  DOCKER_SOCKET=/var/run/docker.sock
  ```

- **HOSTNAME**  
  *Description:* The hostname for the **[Traefik](https://doc.traefik.io/traefik/)** service. This is used in extra hosts and healthchecks.  
  *Example:*  
  ```env
  HOSTNAME=proxy.example.com
  ```

- **PROXY_NETWORK_IPV4_CIDR**  
  *Description:* IPv4 CIDR for the proxy's Docker network. Used to define the internal private subnet.  
  *Default:* `10.0.0.0/24`  
  *Example:*  
  ```env
  PROXY_NETWORK_IPV4_CIDR=10.0.0.0/24
  ```

- **PROXY_NETWORK_IPV6_CIDR**  
  *Description:* IPv6 CIDR for the proxy's Docker network. Used to define the internal private subnet.  
  *Default:* `fd00::/64`  
  *Example:*  
  ```env
  PROXY_NETWORK_IPV6_CIDR=fd00::/64
  ```

- **SSL_LABS_IPV4_CIDR**  
  *Description:* The IPv4 CIDR block used for **[SSL Labs](https://www.ssllabs.com/ssltest/)** certificate validation.  
  *Default:* `64.41.200.0/24`  
  *Example:*  
  ```env
  SSL_LABS_IPV4_CIDR=64.41.200.0/24
  ```

- **SSL_LABS_IPV6_CIDR**  
  *Description:* The IPv6 CIDR block used for **[SSL Labs](https://www.ssllabs.com/ssltest/)** certificate validation.  
  *Default:* `2600:c02:1020:4202::/64`  
  *Example:*  
  ```env
  SSL_LABS_IPV6_CIDR=2600:c02:1020:4202::/64
  ```

- **TRAEFIK_PRIVATE_IPV4**  
  *Description:* Static IPv4 address assigned to the Traefik container within the defined proxy network private subnet.  
  *Default:* `10.0.0.2`  
  *Example:*  
  ```env
  TRAEFIK_PRIVATE_IPV4=10.0.0.2
  ```

- **TRAEFIK_PRIVATE_IPV6**  
  *Description:* Static IPv6 address assigned to the Traefik container within the defined proxy network private subnet.  
  *Default:* `fd00::a00:2`  
  *Example:*  
  ```env
  TRAEFIK_PRIVATE_IPV6=fd00::a00:2
  ```

## Usage

1. **Open Required Ports:**  
   Before cloning the repository, ensure that your firewall allows traffic on ports 80 and 443:
    ```sh
    sudo ufw route allow proto tcp from any to any port 80
    sudo ufw route allow proto tcp from any to any port 443
    ```

2. **Clone the Repository:**  
   Clone the **Container Proxy** repository and navigate into its directory:
    ```sh
    git clone https://github.com/cloud-skeleton/container-proxy.git
    cd container-proxy
    ```

3. **Create a `.env` File:**  
   Place a `.env` file in the repository root with the required variables:
    ```env
    ADMIN_ALLOW_IP_CIDR=203.0.113.42/32
    CERTIFICATE_EMAIL=admin@example.com
    DOCKER_SOCKET=/var/run/docker.sock
    HOSTNAME=proxy.example.com
    PROXY_NETWORK_IPV4_CIDR=10.0.0.0/24
    PROXY_NETWORK_IPV6_CIDR=fd00::/64
    SSL_LABS_IPV4_CIDR=64.41.200.0/24
    SSL_LABS_IPV6_CIDR=2600:c02:1020:4202::/64
    TRAEFIK_PRIVATE_IPV4=10.0.0.2
    TRAEFIK_PRIVATE_IPV6=fd00::a00:2
    ```

4. **Deploy with [Docker Compose](https://docs.docker.com/compose/gettingstarted/):**  
   Run the following command to start the services:
    ```sh
    docker compose up -d
    ```

5. **Automate Updates:**  
   To ensure the latest `security.txt` is always served, set up a cron job that automatically performs a `git pull` in the current repository on the first day of every month at 06:00 UTC. You can create the cron job by running the following shell command as root:

   ```sh
   cat << EOF | sudo tee /etc/cron.d/container-proxy-update > /dev/null
   0 6 1 * * ${USER} git -C "$(pwd)" pull
   EOF
   ```

   This command creates a cron file at `/etc/cron.d/container-proxy-update` that will run the `git pull` command in the current directory (as determined by `$(pwd)`) at the specified time.

6. **Verify the Security File:**  
   Use the provided public **[GPG](https://www.gnupg.org/gph/en/manual.html)** key (`info@cloudskeleton.eu.public.asc`) to verify the signature of `security.txt`.

## External Integration & DNS Setup

- **Integration with Other Compose Files:**  
  It is expected that users may run other compose files on the same machine. For these setups:
  - Create an external network named `proxy_bridge` and attach your services to this network.
  - **Do not expose ports directly in your service definitions; only the **[Traefik](https://doc.traefik.io/traefik/)** service should expose ports.**
  - Control service exposure using simple **[Traefik](https://doc.traefik.io/traefik/)** labels. For example, in your own `compose.yml`:

  ```yaml
  networks:
    proxy_bridge:
      external: true

  services:
    my-service:
      image: my-service-image
      networks:
        - proxy_bridge
      labels:
        - traefik.enable=true
        - traefik.http.routers.customRouter.rule=Host("www.domain.com")
  ```

- **DNS Configuration:**  
  Before using the reverse proxy, ensure that you set up the required DNS A record pointing your domain (e.g., `proxy.example.com`) to the IP address of the host running the **[Container Proxy](https://github.com/cloud-skeleton/container-proxy/)**. This is essential for proper routing of external traffic.

- **[Traefik](https://doc.traefik.io/traefik/) Dashboard Access:**  
  The **[Traefik](https://doc.traefik.io/traefik/)** dashboard is accessible at the `/traefik` endpoint but only from the IP range specified in the `ADMIN_ALLOW_IP_CIDR` variable.

## Contributing

Contributions are welcome!  
- Fork the repository.
- Create a new branch (e.g., **`feature/my-improvement`**).
- Submit a pull request with your changes.

## License

This project is licensed under the [GNU General Public License v3.0](LICENSE).

---

*This repository is maintained exclusively by the **[Cloud Skeleton](https://github.com/cloud-skeleton/)** project, and it was developed by EU citizens who are strong proponents of the European Federation. 🇪🇺*
