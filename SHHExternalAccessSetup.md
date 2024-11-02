# Complete Guide to Setting Up an SSH Tunnel with Firewall Configuration

This guide walks you through setting up an SSH tunnel to securely expose a web application running on port 8080 on a server while restricting access using firewalls at both the local (server) and router levels.

## Prerequisites

- A server with SSH access (running a Linux distribution).
- An application running on port 8080 on the server (e.g., a web server).
- SSH client installed on the client machine.
- Administrative access to the router for firewall configuration.

## Step 1: Set Up the Application on the Server

1. **SSH into the server**:
   ```bash
   ssh user@server_ip
   ```
   Replace `user` with your username and `server_ip` with your server's IP address.

2. **Start your application on port 8080**. For example, using Python:
   ```bash
   python3 -m http.server 8080
   ```

## Step 2: Configure the Local Firewall on the Server

### Using `ufw` (Uncomplicated Firewall)

`ufw` is one option for configuring the firewall on your server. If you have an existing firewall system (like `iptables` or `firewalld`), you can adapt the steps accordingly. Here’s how to configure `ufw`:

1. **Install `ufw`** (if not already installed):
   ```bash
   sudo apt update
   sudo apt install ufw
   ```

2. **Enable the firewall**:
   ```bash
   sudo ufw enable
   ```

3. **Allow access to port 8080** (for your application):
   ```bash
   sudo ufw allow 8080/tcp
   ```

4. **Deny access to other ports** (optional, depending on your requirements):
   - Allow SSH access only from specific client IPs:
     ```bash
     sudo ufw allow from <client_ip> to any port 22
     ```

5. **Deny access to other common ports if needed**:
   ```bash
   sudo ufw deny 80/tcp
   sudo ufw deny 443/tcp
   ```

6. **Check the status of the firewall to ensure rules are set**:
   ```bash
   sudo ufw status
   ```

### General Explanation of `ufw`

- **`ufw` is designed to be an easy-to-use interface for managing firewall rules**. By default, it denies all incoming connections and allows outgoing connections. You explicitly allow connections to specific ports (like port 8080) or from specific IP addresses.
- **If you already have a firewall solution in place**, you may need to configure it to allow access to port 8080 while restricting other ports according to your security needs.

## Step 3: Configure the Router Firewall

Modifying your router's firewall settings is essential for allowing external traffic to reach your server. Here’s how to do it:

1. **Log in to your router**: Open a web browser and enter the router's IP address (commonly `192.168.1.1` or `192.168.0.1`). Enter the admin username and password.

2. **Locate the port forwarding section**: This may be under different names depending on your router (e.g., Port Forwarding, Virtual Server, Applications).

3. **Set up a port forwarding rule**:
   - **Service Name**: Name it something meaningful, like "SSH_Tunnel".
   - **Protocol**: Select TCP.
   - **External Port**: Enter `8080`.
   - **Internal IP Address**: Enter the local IP address of your server (e.g., `192.168.1.100`).
   - **Internal Port**: Enter `8080`.
   - **Enable**: Make sure the rule is enabled.

4. **Save your settings** and reboot the router if necessary.

### General Explanation of Router Firewall Modifications

- **Router firewalls control incoming and outgoing traffic** to and from your local network. By configuring port forwarding, you direct specific external requests (e.g., from clients wanting to access your application) to the correct internal device (your server).
- **Always ensure that you are only forwarding necessary ports** to minimize security risks. Restrict access based on IP when possible.

## Step 4: Create the SSH Tunnel from the Client

1. **On your client machine**, open a terminal.

2. **Run the SSH command to create the tunnel**:
   ```bash
   ssh -L 8080:localhost:8080 user@server_ip
   ```
   - `-L 8080:localhost:8080`: This forwards local port 8080 to port 8080 on the server.
   - `user@server_ip`: Your SSH credentials for the server.

## Step 5: Access the Application

1. **Open a web browser on the client machine**.

2. **Navigate to**:
   ```plaintext
   http://localhost:8080
   ```

## Additional Security Measures (Optional)

- **Use SSH keys instead of passwords** for added security:
  - Generate SSH keys on the client:
    ```bash
    ssh-keygen
    ```
  - Copy the public key to the server:
    ```bash
    ssh-copy-id user@server_ip
    ```

- **Consider setting up a VPN** if you have multiple clients needing access to more restricted resources.

## Summary

1. **Start your application on port 8080** on the server.
2. **Configure the local firewall** using `ufw` (or another firewall solution) to allow traffic only to the necessary ports.
3. **Modify the router's firewall** to forward external traffic on port 8080 to your server.
4. **Create an SSH tunnel** from the client to the server, forwarding local traffic to the application port.
5. **Access your application** via `http://localhost:8080`.

This setup securely exposes your application on port 8080 while limiting access to other ports on the server and the network.
