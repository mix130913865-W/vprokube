# VProfile Microservices Platform

## Kubernetes & Docker-based Deployment Architecture

---

## 1. 專案目的（Purpose）

本專案示範一個 **多服務（Multi-tier）Web Application** 如何使用：

- **Docker**（含 Multi-stage Build）
- **Kubernetes**（Deployment / Service / Ingress / PVC / Secret）
- **Nginx / Tomcat / MySQL / Memcached / RabbitMQ**

完成一套 **可部署、可擴展、符合雲端實務的系統架構**。

### 設計目標

- 模擬企業內部微服務部署情境  

---

## 2. Repository 結構說明

```text
.
├── Docker-files/
│   ├── app/
│   │   ├── Dockerfile              # Application container（單階段）
│   │   └── multistage/
│   │       └── Dockerfile          # Application Multi-stage build
│   │
│   ├── db/
│   │   └── Dockerfile              # MySQL Database image
│   │
│   └── web/
│       ├── Dockerfile              # Nginx reverse proxy
│       ├── nginxvproapp.conf       # Nginx app routing config
│       └── d.txt                   # 測試 / 文件用途檔案
│
├── kubedefs/
│   ├── appdeploy.yaml              # vproapp Deployment
│   ├── appservice.yaml             # vproapp Service
│   ├── appingress.yaml             # Ingress (NGINX)
│   │
│   ├── dbdeploy.yaml               # MySQL Deployment
│   ├── dbpvc.yaml                  # PersistentVolumeClaim
│   ├── dbservice.yaml              # MySQL Service
│   │
│   ├── mcdep.yaml                  # Memcached Deployment
│   ├── mcservice.yaml              # Memcached Service
│   │
│   ├── rmqdeploy.yaml              # RabbitMQ Deployment
│   ├── rmqservice.yaml             # RabbitMQ Service
│   │
│   └── secret.yaml.example         # Secret 範本（不直接 commit）
│
└── docker-compose.yml              # Local 開發 / 測試用途
```
## 3. 系統整體架構圖（Architecture Diagram）
### 3.1 對外存取流程（Ingress Flow）
```
        Client / Browser
               |
        vprofile.lifecoachvida.com
               |
        +------------------+
        |  NGINX Ingress   |
        +------------------+
               |
        vproapp-service (ClusterIP)
               |
          vproapp Pod
```
### 3.2 內部服務關聯架構
```
                     +-------------------+
                     |   vproapp Pod     |
                     |  (App / Tomcat)   |
                     +---------+---------+
                               |
        ------------------------------------------------
        |                     |                        |
  vprodb Service       vprocache Service        rmq Service
        |                     |                        |
  MySQL Pod           Memcached Pod           RabbitMQ Pod
        |
   PersistentVolume
```
