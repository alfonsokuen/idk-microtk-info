<p align="center">
  <img src="https://idkmanager.com/wp-content/uploads/2023/01/cropped-idk-manager-LOGO-1-1-1.png" alt="IDKmanager" width="80"/>
</p>

<h1 align="center">IDK Microtk</h1>

<p align="center">
  <strong>SaaS multi-tenant para gestión de hotspots MikroTik RouterOS v7+</strong>
</p>

<p align="center">
  <a href="https://microtk.idkmanager.com/"><img src="https://img.shields.io/badge/live-microtk.idkmanager.com-1f6feb?style=flat" alt="Live"/></a>
  <img src="https://img.shields.io/badge/RouterOS-v7%2B-orange?style=flat" alt="RouterOS"/>
  <img src="https://img.shields.io/badge/multi--tenant-yes-green?style=flat" alt="Multi-tenant"/>
  <img src="https://img.shields.io/badge/license-proprietary-lightgrey?style=flat" alt="License"/>
</p>

---

## ¿Qué es IDK Microtk?

Plataforma SaaS para WISPs, ISPs y operadores de hotspots que usan routers MikroTik. Centraliza administración multi-router, generación de vouchers, cobros, sesiones en vivo, y reportería — sin reemplazar al router, conectándose vía agente liviano que instalás en RouterOS y se sincroniza por long-poll HTTPS hacia la nube.

> **En vivo**: [microtk.idkmanager.com](https://microtk.idkmanager.com/)

## Para quién es

- **WISPs** que gestionan múltiples zonas de hotspot y necesitan vouchers escalables.
- **Hoteles, cafés, coworkings** que quieren ofrecer WiFi controlado con tickets y planes.
- **ISPs pequeños** que quieren reemplazar Mikhmon o builds caseros con algo en la nube, multi-tenant, y con métricas reales.
- **White-label**: agencias o resellers que quieren ofrecer el panel bajo su propio dominio.

## Características principales

| Módulo | Qué hace |
|---|---|
| **Identity** | Auth multi-tenant con JWT RS256 + JWKS, signup/login/refresh, gestión de usuarios y roles. |
| **Fleet** | Gestión multi-router. Agente RouterOS-script (instalación 1-comando) que mantiene long-poll de 25s contra la nube. Sin abrir puertos en el router del cliente. |
| **Plans & Vouchers** | CRUD de planes (rate limit, sesión, dispositivos compartidos, cuota de datos). Generación batch de vouchers (modo `vc` voucher / `up` user+pwd) con códigos QR + impresión térmica 58/80mm. Sincronización fan-out de planes → hotspot-profiles a todos los routers del tenant. |
| **Sessions** | Vista en vivo de sesiones activas + histórico, con kick remoto. |
| **POS / Commerce** | Caja para vendedores (cash sessions, ventas, refunds), pasarela PayPhone integrada, comisiones por vendedor. |
| **Reports** | Dashboard de tenant + reportería de ventas + cierre de caja en PDF. |
| **WhatsApp** | Envío de vouchers a clientes via Evolution API (WhatsApp). |
| **White-label** | Subdominio personalizado (`tu-marca.microtk.idkmanager.com`) o dominio propio (custom certs). |
| **On-prem ready** | Versión enterprise self-hosted con sync continua de licencia (JWT + JWKS + machine-id) y modo degraded read-only en caso de pérdida de conectividad — nunca shutdown. |

## Cómo se conecta al router del cliente

El router NO necesita IP pública ni puertos abiertos. Generás un token de agente en la UI, lo pegás en RouterOS como un script `/system script`, y un scheduler dispara `/tool fetch` cada ~9s contra el endpoint `agent.idkmanager.com`. Comandos pendientes (crear voucher, sincronizar profile, kick session) viajan en la respuesta del long-poll. Pull-only, firewall-friendly.

## Comparación rápida

| | Mikhmon | UISP | IDK Microtk |
|---|:-:|:-:|:-:|
| Multi-tenant | ✗ | ✓ | ✓ |
| Multi-router por tenant | ✗ | ✓ | ✓ |
| Sin abrir puertos en el router | ✓ | ✗ | ✓ |
| Vouchers batch + QR + impresión térmica | ✓ | ✗ | ✓ |
| POS + comisiones de vendedor | ✗ | ✗ | ✓ |
| WhatsApp integrado | ✗ | ✗ | ✓ |
| White-label dominio propio | ✗ | parcial | ✓ |
| On-prem disponible | n/a | ✗ | ✓ |

## Arquitectura (alto nivel)

```
                  microtk.idkmanager.com (web admin)
                            │
                            ▼
   ┌────────────────────────────────────────────────┐
   │   identity      fleet (long-poll)   commerce   │
   │   (JWT/JWKS)    (router agents)     (POS/pay)  │
   └────────────────────────────────────────────────┘
                            │
                            ▼
                    Postgres HA + Redis HA
                            │
                            ▼
   long-poll ◄───────── agente RouterOS (1 script)
        │                         │
        ▼                         ▼
   tu router MikroTik       hotspot del cliente final
```

- 3 servicios NestJS (identity / fleet / commerce) + 1 web admin Next.js 14
- Auth JWT RS256 con JWKS rotables
- Multi-schema Postgres con RLS por tenant
- Agente RouterOS de un solo script, sin dependencias

## Estado actual

- **MVP Phase 1**: 🟢 LIVE en producción
- **F1 — Hardening**: en curso
- **F2 — WhatsApp Bot + portal pulido**: planificado
- **F3 — Enterprise on-prem**: planificado

## Probarlo

Visitá [microtk.idkmanager.com](https://microtk.idkmanager.com/) y creá una cuenta. La versión SaaS pública es de evaluación.

Para evaluación enterprise / on-prem / white-label: contactar.

## Contacto

- **Web**: [idkmanager.com](https://idkmanager.com/)
- **Email**: alfonsok@gmail.com

---

> Este repositorio contiene **información pública del producto**. El código fuente es privado.
