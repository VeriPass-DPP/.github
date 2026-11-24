# Plataforma SaaS de Pasaporte Digital de Producto (DPP)

> **Infraestructura B2B de autoservicio para pymes fabricantes** orientada al cumplimiento del **Reglamento Europeo de Diseño Ecológico para Productos Sostenibles — Reglamento (UE) 2024/1781 (ESPR)**.

---

## 🎯 ¿Qué problema resuelve este proyecto?

La legislación europea exige que los productos físicos comercializados en el mercado único incorporen un **Pasaporte Digital de Producto (DPP)** accesible mediante portador físico (código QR), con datos estructurados legibles por máquina, control de acceso por roles y respaldo independiente ante insolvencia.

Las soluciones actuales del mercado se dividen entre softwares industriales inaccesibles para pymes (> 15.000 €/año y meses de implantación) y generadores básicos de QRs estáticos que incumplen la normativa europea. **Esta plataforma cierra esa brecha** ofreciendo un modelo **100 % autoservicio** enfocado inicialmente en el sector **Textil, Calzado y Textil-Hogar**:

- **Ingesta documental asistida por IA:** Extracción automática de materiales, porcentajes y certificaciones (GOTS, OEKO-TEX, GRS) desde fichas técnicas PDF mediante Google Gemini API con validación humana obligatoria.
- **Semáforo regulatorio preventivo:** Validación automática de dígito de control GTIN, suma de fibras al 100 %, alertas químicas (PFHxA), cumplimiento de alegaciones ambientales (Directiva EmpCo) y diligencia debida EUDR.
- **Estándares abiertos e interoperabilidad:** Implementación nativa de **GS1 Digital Link** (`/01/{gtin}/10/{lote}`) con negociación de contenido HTTP (`text/html` para consumidor móvil frente a `application/ld+json` estructurado bajo esquemas **TracePath / UNTP**).
- **Privacidad y control de acceso por rol (RBAC):** Visibilidad diferenciada para Consumidor Público, Profesional Tier 2 (talleres de reparación y plantas de reciclaje) y Autoridades/Aduanas.
- **Autenticidad sin fricción:** Firma criptográfica nativa **Ed25519 + SHA-256** verificable en navegador y almacenamiento híbrido (**Cloudflare R2 / S3** + **IPFS**), sin requerir *wallets* ni conocimientos Web3 al fabricante o al comprador.
- **Portabilidad y respaldo legal (Art. 10(4) ESPR):** Generación de paquetes ZIP portables verificables y exportación anual agregada de materiales para el SCRAP textil español (**Re-viste**).

---

## 🏗️ Arquitectura del Ecosistema (Repositorios)

El sistema está desacoplado en tres repositorios principales bajo arquitectura hexagonal y contenedores Docker:

| Repositorio | Responsabilidad | Stack Tecnológico |
| :--- | :--- | :--- |
| **[`dpp-backend`](./dpp-backend)** | API REST *multi-tenant*, aislamiento de datos a nivel de fila (RLS), motor normativo, resolvedor GS1 Digital Link, caché de lecturas públicas, firma Ed25519 e ingesta documental con IA. | Java 21, Spring Boot 3, Spring Security, Flyway, PostgreSQL 16, Redis 7, Gemini API |
| **[`dpp-frontend`](./dpp-frontend)** | Panel B2B de autoservicio para pymes (dashboard de cumplimiento, asistente con visor PDF/IA, matriz de privacidad, generador de etiquetas QR) y visor público *mobile-first*. | Angular 18+ (Standalone Components, Signals), Tailwind CSS, TypeScript |
| **[`dpp-infrastructure`](./dpp-infrastructure)** | Definición de entornos local y producción, orquestación de bases de datos, emulador S3 (MinIO), proxy inverso y automatización de copias de seguridad. | Docker, Docker Compose, Nginx, MinIO (S3) |

---

## 🔄 Flujo Técnico de Alto Nivel

```text
[Pyme Fabricante] ──(Sube ficha PDF / Datos)──► [dpp-frontend (Angular)]
                                                        │
                                                   API REST (JWT)
                                                        ▼
[PostgreSQL 16 (RLS)] ◄──(Aislamiento Tenant)── [dpp-backend (Spring Boot 3)]
       │                                                ├──► [Gemini API] (Extracción JSON estructurado)
       │                                                ├──► [R2 / MinIO] (Imágenes y QRs SVG/PDF)
       │                                                ├──► [IPFS / Pinata] (JSON-LD y Certificados CID)
       ▼                                                └──► [Redis 7] (Caché vista pública móvil)
[Etiqueta QR GS1] ──(Escaneo Móvil / Inspector)──► [Resolvedor HTTP: text/html | application/ld+json]
```

---

## 📚 Marco Normativo y Estándares de Referencia

- **Reglamento (UE) 2024/1781 (ESPR):** Reglamento de diseño ecológico para productos sostenibles (Cap. III, Arts. 9–15 y Anexo III).
- **Reglamento de Ejecución (UE) 2026/1778:** Funcionamiento del Registro Central europeo de Pasaportes Digitales de Producto.
- **GS1 Digital Link (ISO/IEC 18975):** Identificación persistente y resolución web estándar.
- **CIRPASS-2 / TracePath & UNTP:** Modelos semánticos abiertos (JSON Schema y JSON-LD) para interoperabilidad textil y de calzado.

---

## 👤 Autoría

Proyecto diseñado y desarrollado por **José García** — *Trabajo de Fin de Ciclo en Desarrollo de Aplicaciones Web (DAW), 2026*.
