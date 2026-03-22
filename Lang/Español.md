<div align="center">

<img src="https://img.shields.io/badge/FlowPay-Sync-6366f1?style=for-the-badge&logoColor=white" alt="FlowPay Sync"/>

# FlowPay Sync

### Agente de Ventas Omnicanal con IA para LATAM

*Conectando el caos de WhatsApp con el orden de Clover (Fiserv)*

[![Next.js](https://img.shields.io/badge/Next.js-16-black?style=flat-square&logo=next.js)](https://nextjs.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-5-3178C6?style=flat-square&logo=typescript)](https://www.typescriptlang.org)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind-3-38BDF8?style=flat-square&logo=tailwindcss)](https://tailwindcss.com)
[![Claude AI](https://img.shields.io/badge/Claude-AI-d97706?style=flat-square)](https://anthropic.com)
[![Clover](https://img.shields.io/badge/Clover-Fiserv-22c55e?style=flat-square)](https://clover.com)
[![Fiserv](https://img.shields.io/badge/Fiserv-Loyalty-00a651?style=flat-square)](https://fiserv.com)

---

> **Hackathon Aleph · Track Fiserv/Clover**
>
> El 70% de las ventas en PyMEs LATAM ocurren por WhatsApp.
> Su POS de Clover no se entera — hasta ahora.

</div>

---

## Índice

- [El Problema](#el-problema)
- [La Solución](#la-solución)
- [Features Clave](#features-clave)
- [Arquitectura del Sistema](#arquitectura-del-sistema)
- [Stack Tecnológico](#stack-tecnológico)
- [Estructura del Proyecto](#estructura-del-proyecto)
- [Instalación y Setup](#instalación-y-setup)
- [Variables de Entorno](#variables-de-entorno)
- [API Reference](#api-reference)
- [Modificaciones de Frontend](#modificaciones-de-frontend)
- [Demo en Vivo](#demo-en-vivo)
- [Roadmap](#roadmap)

---

## El Problema

Las PyMEs en LATAM viven una contradicción diaria:

| Canal | Realidad |
|---|---|
| **WhatsApp** | +60% de ventas se gestionan aquí, en texto desordenado |
| **Clover POS** | Sistema formal, integrado con Fiserv, pero aislado de redes sociales |
| **El cajero** | Intermediario manual que copia pedidos de WhatsApp al POS → errores, pérdida de tiempo, sin trazabilidad |

**Resultado:** Revenue sin registrar, inventario desincronizado, cero datos para decisiones.

---

## La Solución

FlowPay Sync 2.0 es un **Agente de Ventas Autónomo** de tres capas:

```
Cliente en WhatsApp
        │
        ▼  Mensaje caótico en lenguaje natural
┌───────────────────────────────────────────┐
│           FLOWPAY SYNC 2.0                │
│                                           │
│  ┌─────────┐   ┌──────────┐   ┌────────┐ │
│  │ Claude  │──▶│ Clover   │──▶│Loyalty │ │
│  │   AI    │   │  Fiserv  │   │ Fiserv │ │
│  └─────────┘   └──────────┘   └────────┘ │
│   Parsea orden  Crea orden POS  Loyalty   │
└───────────────────────────────────────────┘
        │
        ▼  Link de pago + confirmación automática
Cliente recibe respuesta en WhatsApp (sin intervención humana)
```

---

## Features Clave

### Feature 1 — Zero-Click AI Bot

> El cajero no toca el teclado. La IA hace todo.

El cliente escribe desde WhatsApp:

```
"Che, mandame 2 lomos con fritas a la calle San Martín 123,
y sacale la mayo a uno"
```

FlowPay Sync, en milisegundos y **sin intervención humana**:

1. Recibe el mensaje vía Twilio → `POST /api/whatsapp-webhook`
2. Claude AI parsea el texto caótico a JSON estructurado
3. Crea la orden en el POS Clover vía REST API
4. Genera el link de pago con upsell personalizado
5. Responde automáticamente al cliente con la URL

El cajero solo observa cómo la orden *aparece* en el Dashboard.

---

### Feature 2 — Sincronización Bidireccional Clover

> FlowPay no reemplaza a Clover, lo potencia.

```
Cliente paga el link  ──▶  Webhook Fiserv  ──▶  Dashboard PAID ✓
                                    │
                                    └──▶  Clover POS marcado
                                          "Listo para preparar"
```

- Dashboard Next.js actualiza en **< 3 segundos** vía polling automático
- La orden aparece en la pantalla física del POS sin acción manual
- Clover ID queda trazado en cada transacción del historial

---

### Feature 3 — Fiserv Loyalty Rewards

> Conecta ventas digitales con tráfico físico. Dos mundos en uno.

Al completar el pago exitosamente:

- FlowPay registra un descuento de lealtad vía **Fiserv Loyalty**
- El cliente ve en pantalla: *"Ganaste un reward de lealtad — muéstralo en tu próxima visita para un café gratis"*
- Genera tráfico peatonal de vuelta al POS Clover físico

**Por qué es brillante:** Une WhatsApp (digital) → Fiserv Loyalty (rewards) → Clover (físico).

---

## Arquitectura del Sistema

```
                          ┌────────────────────────────────────────┐
                          │            NEXT.JS 16 (puerto 3000)     │
                          │                                          │
  WhatsApp ──▶ Twilio ──▶ │  /api/whatsapp-webhook  (Claude AI)    │
                          │                                          │
  Pago cliente ─────────▶ │  /api/webhook           (Fiserv sync)  │
                          │                                          │
  Dashboard polling ────▶ │  /api/metrics           (3s interval)  │
                          │  /api/transactions      (3s interval)  │
                          │                                          │
  Crear link ───────────▶ │  /api/create-link       (upsell IA)    │
                          └──────────────┬─────────────────────────┘
                                         │
                    ┌────────────────────┼────────────────────┐
                    ▼                    ▼                     ▼
             ┌──────────┐        ┌─────────────┐      ┌───────────────┐
             │  Clover  │        │  lib/store  │      │    Fiserv     │
             │  Fiserv  │        │ (in-memory) │      │   Loyalty     │
             │ Sandbox  │        │             │      │   Rewards     │
             └──────────┘        └─────────────┘      └───────────────┘
```

**Principio central:** Sin servidor externo. Todo el backend vive como API Routes en el mismo proceso de Next.js. Mismo puerto, zero CORS, deploy en un solo comando.

---

## Stack Tecnológico

### Frontend
| Tecnología | Uso |
|---|---|
| **Next.js 16** (App Router) | Framework principal |
| **TypeScript** | Tipado estático |
| **Tailwind CSS** | Estilos utilitarios |
| **Framer Motion** | Animaciones profesionales |
| **Recharts** | Gráficas del dashboard |
| **Radix UI** | Componentes accesibles |

### Backend (API Routes)
| Tecnología | Uso |
|---|---|
| **Next.js API Routes** | 6 endpoints serverless |
| **Claude AI (Anthropic)** | Parseo de pedidos en lenguaje natural |
| **Clover REST API v3** | Creación de órdenes en POS |
| **Fiserv Webhooks** | Confirmación de pagos |
| **Twilio** | Gateway de WhatsApp |

### Fiserv Loyalty
| Tecnología | Uso |
|---|---|
| **Fiserv Loyalty API** | Programa de rewards y lealtad |
| **Clover REST API v3** | Integración de loyalty en POS |

---

## Estructura del Proyecto

```
flowpay-sync/
│
├── app/
│   ├── api/                          ← Backend completo (API Routes)
│   │   ├── create-link/
│   │   │   └── route.ts              ★ Motor de links + upsell IA
│   │   ├── webhook/
│   │   │   └── route.ts              ★ Corazón: recibe pagos + sync Clover
│   │   ├── metrics/
│   │   │   └── route.ts              ★ Métricas en tiempo real
│   │   ├── transactions/
│   │   │   └── route.ts              ★ Historial de transacciones
│   │   └── whatsapp-webhook/
│   │       └── route.ts              ★ Zero-Click AI Bot (Claude + Twilio)
│   ├── page.tsx                       (Dashboard principal — sin cambios)
│   └── layout.tsx                     (Layout global — sin cambios)
│
├── components/
│   └── dashboard/
│       ├── create-payment-dialog.tsx  ✏ Conectado a POST /api/create-link
│       ├── stats-cards.tsx            ✏ Polling a /api/metrics cada 3s
│       ├── transactions-table.tsx     ✏ Polling a /api/transactions + Clover ID
│       └── activity-feed.tsx          ✏ Feed en tiempo real
│
├── lib/
│   ├── store.ts                       ★ Estado global en memoria (crear primero)
│   └── utils.ts                       (sin cambios)
│
├── .env.local                         ★ Credenciales (no commitear)
├── package.json
└── tsconfig.json
```

> **Regla de oro del diseño:** Los cambios de frontend son SOLO en lógica de datos, no en JSX ni estilos. El UX con Framer Motion y Recharts queda intacto.

---

## Instalación y Setup

### Prerrequisitos

- Node.js 18+
- pnpm (gestor de paquetes del proyecto)

### 1. Clonar e instalar

```bash
git clone https://github.com/tu-org/flowpay-sync.git
cd flowpay-sync

# Instalar dependencias base
pnpm install

# Agregar dependencias nuevas del backend
pnpm add @anthropic-ai/sdk
pnpm add twilio
```

### 2. Configurar variables de entorno

```bash
cp .env.example .env.local
# Editar .env.local con tus credenciales (ver sección Variables de Entorno)
```

### 3. Orden de implementación

> Seguir este orden es crítico. `lib/store.ts` debe existir antes que cualquier API route.

```bash
# Paso 1: Estado global (PRIMERO Y OBLIGATORIO)
# Crear lib/store.ts — sin este archivo, todos los routes fallan

# Paso 2: API Routes del backend
# Crear app/api/create-link/route.ts
# Crear app/api/webhook/route.ts
# Crear app/api/metrics/route.ts
# Crear app/api/transactions/route.ts

# Paso 3: Modificar componentes del dashboard
# create-payment-dialog.tsx — conectar al API real
# stats-cards.tsx — polling de métricas
# transactions-table.tsx — tabla en vivo
# activity-feed.tsx — feed en tiempo real

# Paso 4: Iniciar
pnpm dev
```

### 4. Verificar que todo funciona

```bash
# El webhook debe responder:
curl http://localhost:3000/api/webhook
# ✓ {"status":"FlowPay webhook activo ✓"}

# Las métricas deben retornar JSON:
curl http://localhost:3000/api/metrics
# ✓ {"totalRevenue":45231.89,"totalSales":2350,...}
```

---

## Variables de Entorno

Crear `.env.local` en la raíz del proyecto.

```env
# ─── BASE ──────────────────────────────────────────────────────
NEXT_PUBLIC_BASE_URL=http://localhost:3000

# ─── CLOVER / FISERV (Sandbox) ─────────────────────────────────
# Sin estas variables: modo simulado automático — OK para demo
CLOVER_MERCHANT_ID=tu_merchant_id_aqui
CLOVER_API_TOKEN=tu_api_token_aqui

# ─── TWILIO WHATSAPP ────────────────────────────────────────────
# Sin estas variables: bot desactivado
TWILIO_SID=ACxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
TWILIO_TOKEN=tu_auth_token
TWILIO_WA=whatsapp:+14155238886

# ─── ANTHROPIC (Claude AI) ──────────────────────────────────────
# Sin esta variable: bot de WhatsApp desactivado
ANTHROPIC_API_KEY=sk-ant-xxxxxxxxxxxxx
```

> **Modo simulado automático:** Si `CLOVER_MERCHANT_ID` no está definido, el webhook devuelve `cloverId: "SIM_xxxxx"`. El dashboard funciona perfectamente sin credenciales externas — ideal para la demo.

---

## API Reference

### `POST /api/create-link`

Genera un link de pago con upsell inteligente por categoría.

**Request:**
```json
{
  "product": "Pizza Margherita",
  "amount": 15.99
}
```

**Response:**
```json
{
  "success": true,
  "link": {
    "id": "fp_1748291234_abc123",
    "product": "Pizza Margherita",
    "amount": 15.99,
    "url": "http://localhost:3000/pay/fp_1748291234_abc123",
    "whatsappUrl": "https://wa.me/?text=...",
    "upsell": "🍹 Agrega bebida por solo +$3.20 — 3× valor percibido",
    "status": "active"
  }
}
```

---

### `POST /api/webhook`

Recibe pagos de Fiserv y sincroniza con Clover POS.

**Request:**
```json
{
  "product": "Pizza Margherita",
  "amount": 15.99,
  "customer": "Juan Pérez",
  "email": "juan@demo.com",
  "status": "completed",
  "paymentMethod": "Clover POS"
}
```

**Response:**
```json
{
  "success": true,
  "transactionId": "TXN-001",
  "cloverId": "SIM_1748291234"
}
```

| Comportamiento | Condición |
|---|---|
| Crea orden real en Clover | `CLOVER_MERCHANT_ID` y `CLOVER_API_TOKEN` definidos |
| Devuelve `SIM_xxxxx` | Variables no definidas — modo simulado |
| Devuelve `ERR_xxxxx` | Clover API respondió con error |

---

### `GET /api/webhook`

Verifica que el sistema está activo.

```json
{ "status": "FlowPay webhook activo ✓" }
```

---

### `GET /api/metrics`

Retorna métricas actuales del negocio.

```json
{
  "totalRevenue": 45247.88,
  "totalSales": 2351,
  "activeLinks": 3,
  "uniqueCustomers": 574,
  "revenueChange": 20.1,
  "salesChange": 180.1
}
```

---

### `GET /api/transactions?limit=10&status=completed`

Retorna historial de transacciones.

| Parámetro | Tipo | Default | Descripción |
|---|---|---|---|
| `limit` | number | 10 | Máximo de registros |
| `status` | string | — | Filtrar por `completed`, `pending`, `failed` |

---

### `POST /api/whatsapp-webhook`

Endpoint de Twilio. Recibe mensajes de WhatsApp y ejecuta el bot Zero-Click.

**Flujo interno:**
1. Extrae `Body` y `From` del form-data de Twilio
2. Claude AI parsea el texto a JSON de orden
3. Genera link de pago y responde al cliente vía Twilio API
4. Retorna TwiML vacío `<Response/>` para evitar respuesta duplicada

---

## Modificaciones de Frontend

Los 4 componentes conectados al backend. **El diseño no se toca.**

### `create-payment-dialog.tsx`

```
✏ Paso A: Agregar estados whatsappLink y linkId
✏ Paso B: Reemplazar setTimeout mock por fetch a POST /api/create-link
✏ Paso C: Agregar botón "Share via WhatsApp" en el paso success
```

### `stats-cards.tsx`

```
✏ Agregar hook useMetrics() con polling cada 3000ms a /api/metrics
✏ Reemplazar array hardcodeado de stats por datos dinámicos del hook
```

### `transactions-table.tsx`

```
✏ Reemplazar array estático por useState + polling a /api/transactions
✏ Agregar campo cloverId al tipo Transaction
✏ Agregar columna "Clover" en la tabla con ID truncado en mono verde
```

### `activity-feed.tsx`

```
✏ Agregar polling a /api/transactions mapeado a formato de actividad
✏ Mostrar liveActivities cuando estén disponibles, fallback a activities
```

---

## Demo en Vivo

> Tener estos comandos listos **antes** de subir al escenario. Copiar y pegar, nunca escribir en vivo.

### Verificar que el sistema está activo

```bash
curl http://localhost:3000/api/webhook
```
```json
{"status":"FlowPay webhook activo ✓"}
```

### Simular Pago 1 — Primera actualización del dashboard

```bash
curl -X POST http://localhost:3000/api/webhook \
  -H "Content-Type: application/json" \
  -d '{
    "product": "Pizza Margherita",
    "amount": 15.99,
    "customer": "Juan Pérez",
    "email": "juan@demo.com",
    "status": "completed",
    "paymentMethod": "Clover POS"
  }'
```

### Simular Pago 2 — Mostrar polling en tiempo real

```bash
curl -X POST http://localhost:3000/api/webhook \
  -H "Content-Type: application/json" \
  -d '{
    "product": "Café Americano",
    "amount": 4.50,
    "customer": "Ana García",
    "email": "ana@demo.com",
    "status": "completed",
    "paymentMethod": "WhatsApp"
  }'
```

### Ver métricas actualizadas

```bash
curl http://localhost:3000/api/metrics
curl http://localhost:3000/api/transactions?limit=5
```

### Exponer con ngrok para WhatsApp Bot

```bash
# En una terminal separada:
ngrok http 3000
# Copiar la URL https://xxx.ngrok.io
# Pegarla en Twilio Sandbox Webhook URL
```

### Checklist de verificación antes de la demo

- [ ] `pnpm dev` sin errores en consola
- [ ] `http://localhost:3000/api/metrics` devuelve JSON válido
- [ ] Stats cards muestran datos reales del endpoint
- [ ] Modal "Create Link" genera URL real con botón de WhatsApp
- [ ] Tabla de transacciones se actualiza en < 3s con el curl de pago
- [ ] Métricas `totalRevenue` y `totalSales` aumentan al simular pago

---

## Guion de Demo (3 minutos)

| # | Acción | Impacto Visual |
|---|---|---|
| **1** | Pitch: *"El 70% de sus ventas ocurren en WhatsApp, su POS de Clover no se entera"* | — |
| **2** | Escribir mensaje desordenado desde WhatsApp del cliente | — |
| **3** | Sin tocar el teclado en FlowPay, la orden aparece en el Dashboard | ✨ Magia |
| **4** | WhatsApp del cliente responde solo con el link de pago | 🤖 AI Bot |
| **5** | Simular pago en el celular (tarjeta de prueba Fiserv) | — |
| **6** | Dashboard cambia de Pendiente → **Pagado** sin recargar | ⚡ Webhook |
| **7** | Mostrar reward de lealtad registrado en Fiserv en pantalla del cliente | 🎁 Loyalty |

---

## Roadmap

### v1.0 — Hackathon Release
- [x] Dashboard Next.js con UX profesional
- [x] API Routes backend integrado (zero servidor externo)
- [x] Sincronización con Clover POS (modo simulado + sandbox real)
- [x] Generación de links de pago con upsell por IA
- [x] Polling en tiempo real cada 3 segundos
- [x] Bot WhatsApp Zero-Click con Claude AI

### v2.0 — Post-Hackathon
- [ ] Fiserv Loyalty Rewards (programa de niveles avanzado)
- [ ] OAuth 2.0 para onboarding multi-merchant
- [ ] Soporte de audio (WhatsApp Voice → IA transcripción)
- [ ] Panel de analytics con histórico persistente (base de datos)
- [ ] Integración con Instagram DMs y Telegram
- [ ] App móvil para el cajero (notificaciones push de nuevas órdenes)

---

<div align="center">

**Hackathon Aleph · Track Fiserv/Clover**

*FlowPay Sync 2.0 — Conectando el caos de WhatsApp con el orden de Clover*

</div>