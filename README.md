# API Gateway
The API gateway is responsible for directing incoming requests to appropriate microservices. It acts as a reverse proxy, providing a single entry point into the backend of the system. This simplifies the client-side logic as the client doesn't need to know about the individual microservices. The current configuration includes load balancing, caching, timeouts, and rate limiting for various upstream services.

## Configuration Details

### 1. Upstream Definitions for Load Balancing
- **Map Service**: Single server configuration.
- **Booking Service**: Load balanced across two servers.
- **User Service**: Single server configuration.
- **Statistics Service**: Single server configuration.

### 2. Cache Configuration
- Separate cache paths are set up for the booking, user, and map services.
- Each service has a dedicated cache zone, a maximum cache size of 10GB, and an inactive time of 60 minutes.

### 3. Rate Limiting Configuration
- A global rate limit of 50 requests per second is set up.
- The limit is applied based on the binary remote address of the client.

### 4. Server Configuration
- Listens on port 80 (HTTP).
- The server name is set to the IP address `192.168.96.193`.
- Various endpoints are configured for different services.
- The statistics service is configured for WebSocket connections.

#### Specific Endpoint Configurations:
- **Clinics (Map Service)**
  - Caching is enabled with a validity of 200 responses for 10 minutes.
  - Rate limiting is applied.
- **Bookings (Booking Service)**
  - Configured with timeout settings suitable for high-traffic.
  - Rate limiting is applied.
  - Caching is commented out but can be enabled if needed.
- **Patients, Dentists, and Admins (User Service)**
  - Rate limiting is applied.
  - Caching is commented out but can be enabled if required.

## Installation
Clone the project:
```
git clone https://git.chalmers.se/courses/dit355/2023/student-teams/dit356-2023-04/api-gateway.git

```
Build and run with Docker:
```
docker build -t api-gateway .
docker run -p 80:80 api-gateway

```
## Future Work
- **Dynamic Caching:** We have tried dynamic caching, but with limited success. NGINX does not support dynamic caching after resources have been updated. This is important because GET requests are more frequent than PATCH, POST, PUT, and DELETE, although caching can also pose a security risk if not done properly.

- **Service Discovery:** Referential decoupling can be achieved by use of a DNS resolver. When a service in the network becomes available, it should register its IP address and port with the DNS server. When the API Gateway wants to communicate with a registered service, it makes a DNS query using the service's domain name. In this way, the API Gatway can locate the addresses of different microservices.
