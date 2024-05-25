# BIND DNS Server with Docker

This repository provides a basic setup for running a BIND DNS server inside a Docker container using Docker Compose.

## Prerequisites

- Docker installed on your machine.
- Docker Compose installed on your machine.

## Setup

1. **Clone the repository:**


2. **Create configuration files:**

   - **`named.conf`:**
     
     Create a `named.conf` file inside a `config` directory with the following content:
     
     ```
     options {
         directory "/var/cache/bind";
         listen-on { any; };
         allow-query { any; };
         recursion no;
     };

     zone "example.com" {
         type master;
         file "/etc/bind/db.example.com";
     };
     ```

   - **`db.example.com`:**
     
     Create a `db.example.com` file inside the `config` directory with the following content:
     
     ```
     $TTL    604800
     @       IN      SOA     ns1.example.com. admin.example.com. (
                                   2         ; Serial
                              604800         ; Refresh
                               86400         ; Retry
                             2419200         ; Expire
                              604800 )       ; Negative Cache TTL
     ;
     @       IN      NS      ns1.example.com.
     @       IN      A       192.168.1.1
     ns1     IN      A       192.168.1.1
     ```

3. **Create a Docker Compose file:**

   Create a `docker-compose.yml` file with the following content:

   ```
   version: '3.8'

   services:
     bind9:
       image: internetsystemsconsortium/bind9:9.16
       container_name: bind9
       volumes:
         - ./config/named.conf:/etc/bind/named.conf
         - ./config/db.example.com:/etc/bind/db.example.com
       ports:
         - "53:53/tcp"
         - "53:53/udp"
       restart: unless-stopped
   ```

4. **Start the Docker container:**

   Navigate to the directory containing your `docker-compose.yml` file and run the following command to start your BIND DNS server:

   ```sh
   docker-compose up -d
   ```

## Testing

You can test your DNS server using `dig` or any other DNS query tool. For example:

```sh
dig @localhost example.com
```

## License

This project is licensed under the MIT License.
