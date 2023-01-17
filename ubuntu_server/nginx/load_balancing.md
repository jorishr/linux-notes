## Nginx as a load balancer
Load balancing is a much broader topic and usually involves multiple servers. However, if you run various NodeJS applications on the same server Nginx may be configured to distribute the load between them automatically. To do so you can add an *upstream* context block to the server configuration:
```bash
http {

    upstream backend_servers {
        server localhost:3001;	# nodejs 1
        server localhost:3002;	# nodejs 2
        server localhost:3003;	# nodejs 3
    }

    server {

        listen 80;
        server_name example.com;

        location / {
            proxy_pass http://backend_servers;
			# all traffic is passed on to the backend_servers upstream
        }
    }
}
```