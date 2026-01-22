---

# 🌐 Traefik Centralized Proxy Architecture

Este proyecto implementa una arquitectura de **Proxy Inverso Centralizado** utilizando **Traefik v3**. Permite desplegar múltiples servicios (Astro, Laravel, etc.) de forma independiente, centralizando el tráfico y la gestión de dominios en un solo punto de entrada.

Es una documentación en la cual se puede comprende como se puede replicar este entorno de trabajo en un sistema operativo windos, utilizando lo que seria WSL + ubuntu + docker + docker descktop + traefik , paso a paso se mostrara como desplegar este entorno y los links de referencia.

Añadir que tambien se cuenta con mas documentación de como desplegar proyectos con tecnologias especificas y configuaciónes especiales.



---

## 🚀 Características Principales

* **Entrypoint Personalizado:** Configurado para escuchar en el puerto `8085`. 
* **Dashboard Visual:** Interfaz de control accesible en el puerto `8081`.
* **Aislamiento:** Red de Docker externa llamada `web` para comunicar contenedores.
* **Escalable:** Añade nuevos proyectos simplemente configurando labels de Docker y modificando una palabar clave que se repite.
---

## 🏗️ Estructura del Proyecto

```Estructura
.
├── traefik/                      # Configuración central del Proxy
│   └── docker-compose.yml
│        └── README.md
└── documentation_about_docker_projects/  # Guías específicas por tecnologias
    ├── astro/                   
    │    └── README.md   # Cómo desplegar proyectos Astro
    ├── laravel/                 
    │    └── README.md  # Cómo desplegar proyectos Laravel
    └── traefik/                 
        └── README.md  # Notas técnicas sobre el núcleo


```

---


## requerimientos

* [wsl](https://learn.microsoft.com/en-us/windows/wsl/instal)
* [Docker par ubuntu](https://docs.docker.com/engine/install/ubuntu/)
* [Docker descktop](https://docs.docker.com/desktop/)

## 🛠️ Inicio Rápido

### 1. Levantar el Proxy Central

Primero debemos poner en marcha el "cerebro" de la arquitectura:

```bash
cd traefik
docker compose up -d

```

* **Dashboard:** [http://localhost:8081](https://www.google.com/search?q=http://localhost:8081)
* **Puerto de Apps:** `8085`

---

## 📚 Documentación de Proyectos

Cada tecnología tiene sus propios requerimientos de red y Docker. Hemos preparado guías detalladas para que despliegues tus apps sin errores:

| Tecnología | Guía de Despliegue |
| --- | --- |
| **Traefik Core** | [Ver notas técnicas](https://github.com/AndresNana21/Traefik-projects-docs/tree/main/traefik) |
| **Astro** | [Ver documentación de Astro](https://www.google.com/search?q=./documentation_about_docker_projects/astro/https://www.google.com/search?q=./documentation_about_docker_projects/astro/) |
| **Laravel** | [Ver documentación de Laravel](https://github.com/AndresNana21/Traefik-projects-docs/tree/main/documentation_about_docker_projects/laravel) |

---

