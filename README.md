# 🍷 Aldeanos Club — Architecture & Infrastructure Showcase

> **Caso de Estudio de Arquitectura y Despliegue en Producción.**
> *Este repositorio documenta la arquitectura de software, infraestructura cloud, contratos de API y estrategias de despliegue para la plataforma de fidelización y beneficios "Aldeanos Club". El código fuente privado del cliente permanece bajo acuerdo de confidencialidad.*

---

## 📸 Resumen Ejecutivo

**Aldeanos Club** es una plataforma desacoplada y distribuida diseñada para gestionar la fidelización de clientes, validación de beneficios en tiempo real mediante códigos QR, analítica comercial y administración multi-tenant.

El sistema fue diseñado bajo principios de **Clean Architecture**, alta disponibilidad, seguridad estricta en endpoints y optimización extrema de rendimiento tanto en el consumo de API como en la experiencia de usuario (WPO/SEO).

---

## 📐 Arquitectura de Sistema & Topología

La infraestructura utiliza un modelo distribuido desacoplando el sitio comercial, la aplicación web de clientes/comercios y el núcleo de servicios backend REST.

```mermaid
flowchart TD
    subgraph Clients["🌐 Clientes & Usuarios"]
        Browser["Navegador Web / Mobile"]
    end

    subgraph EdgeLayer["🌍 CDN & Capa de Entrada (DNS)"]
        WPDomain["aldeanosclub.com\n(WordPress / Hostinger)"]
        AppDomain["app.aldeanosclub.com\n(Next.js App / Vercel)"]
        APIDomain["api.aldeanosclub.com\n(Reverse Proxy / Hetzner)"]
    end

    subgraph VPS["🖥️ VPS Linux (Hetzner)"]
        Caddy["Caddy Server\n(Reverse Proxy + Automatic SSL)"]
        
        subgraph DockerNet["🐳 Red Privada Docker"]
            API["Spring Boot 3 API\n(Java 21 / REST)"]
            DB[(PostgreSQL 16\nDatabase)]
        end
    end

    %% Flow connections
    Browser -->|Tráfico Comercial / SEO| WPDomain
    Browser -->|App de Clientes / Dashboard| AppDomain
    AppDomain -->|Peticiones HTTPS / REST| APIDomain
    
    APIDomain --> Caddy
    Caddy -->|HTTP Proxy Pass (Port 8080)| API
    API -->|Spring Data JPA / HikariCP| DB
