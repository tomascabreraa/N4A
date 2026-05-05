# User Journey — Nutrion4All

---

## FASE 1: Descubrimiento y Registro

### Paso 1 — Descubre el sitio web

Usuario llega al landing, navega FAQ y perfil del nutricionista.

| CTAs | Push | Pull |
|------|------|------|
| SIGNUP | — | — |
| LOGIN | — | — |
| Acepta términos y políticas de privacidad | — | — |

→ Acepta/rechaza uso de cookies
→ Visualiza el contenido de la página web *(landing, perfil nutricionista, términos de uso, quiénes somos)*

**¿Entiende la propuesta de valor?**

- **NO** → ¿Le interesa y quiere intentar entenderle?
  - **SÍ** → Vuelve a visualizar el contenido de la página
  - **NO** → **Abandona**
- **SÍ** → Evalúa Precio / Credenciales → ¿Confía en el servicio?
  - **NO** → **Abandona**
  - **SÍ** → Click en botón *"Suscríbete y agenda tu primera consulta"*

---

### Paso 2 — Registro

| CTAs | Push | Pull |
|------|------|------|
| SUBMIT REGISTRATION | Datos de registro | — |

**Push — Datos de registro**
| Field | Type |
|-------|------|
| `name` | `string` |
| `apellidos` | `string` |
| `email` | `string` |
| `telefono` | `string` |
| `acepta_politica_privacidad` | `boolean` |
| `created_at` | `datetime` |

**¿Completa el registro?**

- **NO** → Abandona el formulario
- **SÍ** → Continúa al Paso 3

---

### Paso 3 — Procesamiento del pago con Stripe

| CTAs | Push | Pull |
|------|------|------|
| STRIPE PORTAL | Datos del pago | Stripe API |

**Pull — Stripe**
| Field | Type |
|-------|------|
| `stripe_api_key` | `string` |
| `payment_element` | `object (Stripe Payment Element)` |
| `stripe_session_id` | `string` |

**Push — Datos del pago**
| Field | Type |
|-------|------|
| `payment_status` | `enum: completado \| fallido \| pendiente` |
| `subscription_id` | `string (Stripe subscription ID)` |
| `stripe_customer_id` | `string` |
| `plan` | `string` |
| `paid_at` | `datetime` |

**¿Pago completado?**

- **NO** → Error de pago / reintento / soporte
- **SÍ** → Avanza a FASE 2

---

## FASE 2: Activación — Exploración Inicial

### Paso 4 — Pago confirmado: Pantalla de éxito + Email de Bienvenida

| CTAs | Push | Pull |
|------|------|------|
| SUBMIT CUESTIONARIO | Email de bienvenida + activación | — |

**Push — Activación**
| Field | Type |
|-------|------|
| `user_id` | `string (UUID)` |
| `subscription_active` | `boolean` |
| `welcome_email_sent` | `boolean` |
| `activated_at` | `datetime` |

---

### Paso 5 — Cuestionario Clínico

| CTAs | Push | Pull |
|------|------|------|
| SUBMIT CUESTIONARIO | Datos clínicos del paciente | — |

**¿Completa la información solicitada?**

- **NO** → Click botón de "Dónde" → Sistema envía recordatorio vía email para completar el onboarding → Usuario suscrito pero no activado
  - Sistema envía recordatorio recurrente: es IMPORTANTE completar el formulario clínico para poder consultar con el nutricionista
- **SÍ** → Sistema envía recordatorio vía email de que puede reservar cita (after 7 days) → Continúa al Paso 6

**Push — Datos clínicos del paciente**

> Mismo esquema de datos que el PULL del perfil del paciente definido en el Admin Journey (Flujo 3, Paso 2). Se envían todos los campos del cuestionario clínico al crear el perfil del paciente en la base de datos.

| Field | Type |
|-------|------|
| `patient_id` | `string (UUID)` |
| `name` | `string` |
| `email` | `string` |
| `peso` | `float (kg)` |
| `altura` | `float (cm)` |
| `sexo` | `enum: M \| F \| Otro` |
| `fecha_nacimiento` | `date` |
| `condiciones_medicas` | `object<boolean fields>` — 17 condiciones Si/No |
| `historial_cirugias` | `object` — boolean + string |
| `habitos_comida` | `object<string fields>` |
| `preferencias_alimenticias` | `object<string \| enum fields>` |
| `hidratacion_suplementos` | `object<string \| integer fields>` |
| `frecuencias_consumo` | `object<integer \| boolean fields>` |
| `historia_peso_actividad` | `object<string \| boolean fields>` |
| `submitted_at` | `datetime` |

---

### Paso 6 — Reserva de primera cita

**¿Hizo clic en "Reservar la primera cita"?**

- **NO** → Usuario suscrito pero no activo
- **SÍ** → ¿Completó el cuestionario clínico?
  - **NO** → Visualización del Cuestionario Clínico → Completar y rellenar el cuestionario obligatoriamente → Regresa al Paso 5
  - **SÍ** → Continúa

| CTAs | Push | Pull |
|------|------|------|
| Seleccionar fecha disponible | Cita agendada | Disponibilidad del nutricionista (calendario) |

**Pull — Disponibilidad del nutricionista**
| Field | Type |
|-------|------|
| `admin_id` | `string (UUID)` |
| `available_slots` | `array<datetime>` |

**Push — Cita agendada**
| Field | Type |
|-------|------|
| `appointment_id` | `string (UUID)` |
| `patient_id` | `string (UUID)` |
| `admin_id` | `string (UUID)` |
| `datetime` | `datetime` |
| `status` | `enum: programada \| completada \| cancelada \| reprogramada` |

---

### Paso 7 — Confirmación y recordatorios

| CTAs | Push | Pull |
|------|------|------|
| — | Email de confirmación + link videollamada | Datos de la cita |

**Pull — Datos de la cita**
| Field | Type |
|-------|------|
| `appointment_id` | `string (UUID)` |
| `datetime` | `datetime` |
| `meeting_link` | `string (URL)` |
| `admin_name` | `string` |

**Push — Confirmación**
| Field | Type |
|-------|------|
| `confirmation_email_sent` | `boolean` |
| `meeting_link_sent` | `boolean` |
| `reminder_24h_sent` | `boolean` |
| `sent_at` | `datetime` |

→ Envío automático del link de la videollamada al correo junto con recordatorio
→ Envío de recordatorio 24h antes

**¿El usuario quiere reprogramar o cancelar?**
- **SÍ** → Regresa a selección de disponibilidad (Paso 6)
- **NO** → Llega el día de la videollamada

---

### Paso 8 — Día de la videollamada

| CTAs | Push | Pull |
|------|------|------|
| — | — | Link de videollamada |

**Pull — Link de videollamada**
| Field | Type |
|-------|------|
| `meeting_link` | `string (URL)` |
| `appointment_id` | `string (UUID)` |
| `datetime` | `datetime` |

**¿Asiste el día de la videollamada?**

- **NO** → No show
- **SÍ** → Primera videollamada con el nutricionista *(presentación del caso, personalización del plan, expectativas del paciente)*
  → Videollamada finalizada con el nutricionista

---

## FASE 3: Bloque de Uso y Seguimiento — Retención

### Dashboard — Página de Inicio

| CTAs | Push | Pull |
|------|------|------|
| MENU → INICIO | — | — |
| MENU → REELS | — | — |
| MENU → TICKETS | — | — |
| MENU → FAQs | — | — |
| MENU → HISTORIAL DE RECURSOS | — | — |
| MENU → AJUSTES | — | — |

---

## Flujo A: Seguimiento y Evolución (Métricas)

### Paso 1 — Recibe plan nutricional y recursos personalizados

→ Va a la sección de Historial de Recursos *(ver Flujo C)*

---

### Paso 2 — MÉTRICAS: Registro de peso y mediciones

| CTAs | Push | Pull |
|------|------|------|
| Registrar métricas | Nuevas métricas | Historial de métricas |

**Push — Nuevas métricas**
| Field | Type |
|-------|------|
| `patient_id` | `string (UUID)` |
| `peso` | `float (kg)` |
| `mediciones` | `object` — cintura, cadera, etc. |
| `fecha_registro` | `date` |
| `registered_at` | `datetime` |

**Pull — Historial de métricas**
| Field | Type |
|-------|------|
| `patient_id` | `string (UUID)` |
| `entries` | `array<{ fecha: date, peso: float, mediciones: object }>` |

**¿Registra métricas?**

- **NO** → Sistema envía reminder email cada 10 días
- **SÍ** → Visualiza evolución / adherencia / progreso

→ Usa recurrente: Dashboard / progreso / nueva cita

---

## Flujo B: Historial de Recursos

### Paso 1 — Historial de Recursos

| CTAs | Push | Pull |
|------|------|------|
| Click en el documento deseado | — | Listado de recursos disponibles |

**Pull — Listado de recursos disponibles**
| Field | Type |
|-------|------|
| `patient_id` | `string (UUID)` |
| `resources` | `array<resource>` |
| `resource.resource_id` | `string (UUID)` |
| `resource.name` | `string` |
| `resource.type` | `string` — plan nutricional, análisis, documento, etc. |
| `resource.file_url` | `string (URL)` |
| `resource.uploaded_at` | `datetime` |

→ Ve listado de recursos disponibles → Abre el documento deseado

---

## Flujo C: FAQs

> Contiene explicaciones y dudas comunes sobre el uso de la app N4A: alimentos, citas, suscripción, etc.
> Ejemplos: *¿Cómo cancelar la suscripción? ¿Qué ejercicio hacer en casa? ¿Cómo usar una cita? ¿Qué pasa si cancelo una cita?*

### Paso 1 — Usuario entra a la sección de FAQs

| CTAs | Push | Pull |
|------|------|------|
| Buscar pregunta | — | Listado de FAQs |

**Pull — Listado de FAQs**
| Field | Type |
|-------|------|
| `faqs` | `array<faq>` |
| `faq.faq_id` | `string (UUID)` |
| `faq.pregunta` | `string` |
| `faq.respuesta` | `string (text)` |
| `faq.categoria` | `string` |

---

### Paso 2 — ¿Está disponible la respuesta a su consulta?

- **NO** → El usuario va a la sección de Tickets *(ver Flujo D)*
- **SÍ** → Busca la pregunta y respuesta de su duda → Lee las preguntas y respuestas por contenido

**¿Encontró la respuesta?**

- **SÍ** → Hace click en la pregunta → Visualiza la respuesta y explicación → **Usuario satisfecho**
- **NO** → El usuario va a la sección de Tickets *(ver Flujo D)*

---

## Flujo D: Tickets

### Paso 1 — Usuario registra su duda

| CTAs | Push | Pull |
|------|------|------|
| Registrar tu duda | Nuevo ticket | — |

**Push — Nuevo ticket**
| Field | Type |
|-------|------|
| `ticket_id` | `string (UUID — generated)` |
| `patient_id` | `string (UUID)` |
| `duda` | `string (text)` |
| `categoria` | `string` |
| `status` | `enum: abierto \| pendiente \| resuelto` |
| `created_at` | `datetime` |

→ Envío de consulta confirmado → Sistema asigna ticket al nutricionista

---

### Paso 2 — Usuario espera respuesta

| CTAs | Push | Pull |
|------|------|------|
| — | — | Respuesta del nutricionista |

**Pull — Respuesta del nutricionista**
| Field | Type |
|-------|------|
| `ticket_id` | `string (UUID)` |
| `response_body` | `string (text)` |
| `responded_by` | `string (UUID — admin_id)` |
| `responded_at` | `datetime` |
| `status` | `enum: resuelto \| no_resuelto` |

→ Usuario recibe la respuesta a su duda
→ Descripción: La respuesta del nutricionista aparece en el ticket — los usuarios no pueden volver a responder

---

### Paso 3 — ¿Es satisfactoria la respuesta?

| CTAs | Push | Pull |
|------|------|------|
| Cerrar ticket | Estado del ticket | — |

**Push — Estado del ticket**
| Field | Type |
|-------|------|
| `ticket_id` | `string (UUID)` |
| `satisfecho` | `boolean` |
| `closed_at` | `datetime` |
| `status` | `enum: cerrado \| reabierto` |

- **SÍ** → Cierra el ticket / sigue con mayor confianza → **Usuario satisfecho**
- **NO** → ¿Intento resuelto?
  - **SÍ** → Cierra ticket con insatisfacción registrada
  - **NO** → Consulta considerada no resuelta → Sistema sugiere agendar consulta directamente con el nutricionista

---

## Flujo E: AJUSTES

### Paso 1 — AJUSTES

| CTAs | Push | Pull |
|------|------|------|
| Perfil | — | — |
| Suscripción | — | — |

---

### Paso 2a — Perfil

| CTAs | Push | Pull |
|------|------|------|
| Guardar cambios | Datos de perfil actualizados | Datos de perfil actuales |

**Pull — Datos de perfil actuales**
| Field | Type |
|-------|------|
| `user_id` | `string (UUID)` |
| `name` | `string` |
| `email` | `string` |
| `telefono` | `string` |
| `notification_preferences` | `object<{ email: boolean, push: boolean, reminder_frecuency: integer }>` |

**Push — Datos de perfil actualizados**
| Field | Type |
|-------|------|
| `user_id` | `string (UUID)` |
| `updated_fields` | `object — subset of profile fields` |
| `updated_at` | `datetime` |

**¿Actualiza notificaciones?**

- **SÍ** → Cambia datos de notificaciones → Guarda info → Usuario ve los cambios reflejados
- **NO** → Sin cambios

**Push — Preferencias de notificaciones**
| Field | Type |
|-------|------|
| `user_id` | `string (UUID)` |
| `email_notifications` | `boolean` |
| `push_notifications` | `boolean` |
| `reminder_frecuency_days` | `integer` |
| `updated_at` | `datetime` |

---

### Paso 2b — Suscripción

| CTAs | Push | Pull |
|------|------|------|
| Cancelar | Cambio de suscripción | Datos de suscripción actual |
| Pausar | — | — |
| Cambiar plan | — | — |

**Pull — Datos de suscripción actual**
| Field | Type |
|-------|------|
| `subscription_id` | `string (Stripe subscription ID)` |
| `stripe_customer_id` | `string` |
| `plan` | `string` |
| `status` | `enum: activa \| pausada \| cancelada` |
| `renewal_date` | `date` |
| `price` | `float` |

**Push — Cambio de suscripción**
| Field | Type |
|-------|------|
| `subscription_id` | `string (Stripe subscription ID)` |
| `action` | `enum: cancelar \| pausar \| cambiar_plan` |
| `new_plan` | `string (nullable)` |
| `updated_at` | `datetime` |

**¿Cancela?**

- **SÍ** → Suscripción cancelada → Acceso desactivado al finalizar el período
- **NO** → Cambio de plan o pausa aplicados → Listo
