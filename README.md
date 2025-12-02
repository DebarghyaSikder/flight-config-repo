## Flight Config Repository  
- Centralized configuration repository for the Flight Booking Microservices System.

---

## Overview

- This repository stores **all configuration files** used by the Spring Cloud Config Server for the Flight Booking Microservices platform.  
- Each microservice loads its configuration from this repository at startup, which enables:

- Centralized configuration management  
- Dynamic updates without modifying code  
- Environment-specific configuration  
- Clean separation between code and config  

- The repository is consumed by the **Config Server** running at:

`http://localhost:8888`

---

##  Repository Structure

- flight-config-repo/
- │
- ├── application.yml # Global default config applied to all services
- │
- ├── eureka-server.yml # Eureka Server configuration
- ├── api-gateway.yml # API Gateway routing configuration
- │
- ├── flight-service.yml # Flight Service config
- └── booking-service.yml # Booking Service config

Each file corresponds to the microservice with the same `spring.application.name`.

---

## How Configuration Loading Works

- When a microservice starts, it loads its configuration using:

- `spring.config.import=configserver:http://localhost:8888`

- The Config Server then reads:

- `{application-name}.yml`

- For example:

- | Microservice | Config Loaded From |
- |--------------|--------------------|
- | `flight-service` | `flight-service.yml` |
- | `booking-service` | `booking-service.yml` |
- | `api-gateway` | `api-gateway.yml` |
- | `eureka-server` | `eureka-server.yml` |

---

## Example Configurations

- Below are samples of the types of configurations stored here.

---

## 1. Application-wide defaults (`application.yml`)

```yaml
logging:
  level:
    `root: INFO`

management:
  endpoints:
    web:
      exposure:
        include: "*"
These settings apply to all services unless overridden.

## 2️. Eureka Server (`eureka-server.yml`)

server:
  `port: 8761`

spring:
  application:
    name: eureka-server
## 3️. API Gateway (`api-gateway.yml`)

spring:
  application:
    name: api-gateway

  cloud:
    gateway:
      routes:
        - `id: flight-service`
          `uri: lb://FLIGHT-SERVICE`
          predicates:
            - `Path=/flight/**`

## 4️. Flight Service (flight-service.yml)

server:
  `port: 8090`

spring:
  application:
    name: flight-service

  data:
    mongodb:
      uri: mongodb://localhost:27017/flightdb
## 5️. Booking Service (booking-service.yml)

server:
  `port: 8091`

spring:
  application:
    name: booking-service

  data:
    mongodb:
      `uri: mongodb://localhost:27017/bookingdb`

rabbitmq:
  exchange: booking.exchange
  routing-key: booking.key
  queue: booking.queue