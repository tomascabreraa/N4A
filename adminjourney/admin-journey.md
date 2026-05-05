-# Admin Journey — Nutrion4All

---

## FASE 1: Inicio

**Admin entra al landing**

| CTAs | Push | Pull |
|------|------|------|
| SIGNIN | — | — |

→ Nutricionista accede a Nutrion4All

---

## FASE 2: Bloque de preparación clínica

**Visualización de dashboard inicial**

| CTAs | Push | Pull |
|------|------|------|
| CRM | — | — |
| Gestión de Agenda | — | — |
| Dudas | — | — |
| REELS | — | — |

Dashboard branches into four flows: **Gestión de Agenda**, **CRM**, **Dudas**, and **REELS**.

---

## Flujo 1: Gestión de Agenda

### Paso 1 — Gestión de Agenda

| CTAs | Push | Pull |
|------|------|------|
| Calendario | — | — |
| Ajustar Disponibilidad | — | — |

---

### Paso 1a — Ajustar Disponibilidad

Admin llena formulario para actualizar su disponibilidad.

| CTAs | Push | Pull |
|------|------|------|
| Form | Nueva disponibilidad | — |

**Push — Nueva disponibilidad**
| Field | Type |
|-------|------|
| `schedule_id` | `string (UUID)` |
| `admin_id` | `string (UUID)` |
| `available_slots` | `array<datetime>` |
| `updated_at` | `datetime` |

---

### Paso 1b — Entra en Calendario

| CTAs | Push | Pull |
|------|------|------|
| Enlace de llamada | — | Información de citas (CRM) |
| — | — | Disponibilidad del admin |

**Pull — Información de citas (CRM)**
| Field | Type |
|-------|------|
| `appointment_id` | `string (UUID)` |
| `patient_name` | `string` |
| `patient_id` | `string (UUID)` |
| `datetime` | `datetime` |
| `status` | `enum: programada \| completada \| cancelada` |
| `meeting_link` | `string (URL)` |

**Pull — Disponibilidad del admin**
| Field | Type |
|-------|------|
| `slot` | `datetime` |
| `available` | `boolean` |

→ **Revisa citas del día o semana**

**¿Tiene citas programadas?**

- **NO** → Continúa con otras tareas
- **SÍ** → Se une a la reunión → **Videollamada**

---

## Flujo 2: Dudas

### Paso 1 — Dudas → Tickets

| CTAs | Push | Pull |
|------|------|------|
| — | — | Nombre + Contacto + Contenido |

**Pull — Nombre + Contacto + Contenido**
| Field | Type |
|-------|------|
| `ticket_id` | `string (UUID)` |
| `patient_name` | `string` |
| `email` | `string` |
| `phone` | `string` |
| `ticket_body` | `string (text)` |
| `status` | `enum: abierto \| pendiente \| resuelto` |
| `created_at` | `datetime` |

→ Nutricionista revisa sección de tickets → Ve tickets abiertos o nuevos/pendientes

---

### Paso 2 — ¿Hay tickets nuevos/pendientes?

- **NO** → Continúa con otras tareas
- **SÍ** → Abre ticket y lee la duda del paciente

---

### Paso 3 — ¿La duda se puede resolver por un ticket?

- **NO** → Se rechaza el ticket. El sistema sugiere revisar FAQs o directamente agendar una consulta.
- **SÍ** → Continúa al Paso 4

---

### Paso 4 — Responde el ticket

| CTAs | Push | Pull |
|------|------|------|
| Resuelto | Respuesta | — |
| No resuelto | — | — |

**Push — Respuesta**
| Field | Type |
|-------|------|
| `ticket_id` | `string (UUID)` |
| `response_body` | `string (text)` |
| `status` | `enum: resuelto \| no_resuelto` |
| `responded_by` | `string (UUID — admin_id)` |
| `responded_at` | `datetime` |

→ Sistema guarda respuesta y aparece en la sección de tickets del usuario como ticket respondido.

---

### Paso 5 — ¿El ticket queda resuelto?

- **SÍ** → Se cierra el ticket con mensaje: *"Si no quedas satisfecho, abre un nuevo ticket."*

---

## Flujo 3: CRM / Historial de recursos

### Paso 1 — CRM → Lista de Clientes

| CTAs | Push | Pull |
|------|------|------|
| Filtro | — | — |
| Search | — | — |
| Expandir Cliente | — | — |

Opciones desde Lista de Clientes:
- Filtrar lista por cliente
- Search lista por cliente
- Escoge paciente dentro de la lista del CRM

---

### Paso 2 — Perfil del paciente (datos que se cargan al abrir un paciente)

| CTAs | Push | Pull |
|------|------|------|
| — | — | Perfil completo del paciente |

**Pull — Datos Personales**
| Field | Type |
|-------|------|
| `id` | `string (UUID)` |
| `name` | `string` |
| `email` | `string` |
| `peso` | `float (kg)` |
| `altura` | `float (cm)` |
| `sexo` | `enum: M \| F \| Otro` |
| `fecha_nacimiento` | `date` |
| `recursos_de_paciente` | `array<file (URL)>` |

**Pull — Condiciones médicas** *(Si/No)*
| Field | Type |
|-------|------|
| `alergia_alimentos` | `boolean` |
| `sobrepeso_obesidad` | `boolean` |
| `hipertension_arterial` | `boolean` |
| `dislipemia` | `boolean` |
| `diabetes` | `boolean` |
| `afecciones_cardiovasculares` | `boolean` |
| `estrenimiento` | `boolean` |
| `enf_hepaticas` | `boolean` |
| `enf_aparato_digestivo` | `boolean` |
| `enf_broncopulmonares` | `boolean` |
| `afecciones_osteoarticulares` | `boolean` |
| `afecciones_cutaneas` | `boolean` |
| `anemia` | `boolean` |
| `enfermedad_renal` | `boolean` |
| `trastornos_del_sueno` | `boolean` |
| `depresion_o_ansiedad` | `boolean` |
| `cirugias` | `boolean` |

**Pull — Historial de cirugías y hábitos de comida**
| Field | Type |
|-------|------|
| `intervencion_quirurgica` | `boolean` |
| `tipo_de_cirugia` | `string (text)` |
| `horario_fijo_comidas` | `string (text)` |
| `comidas_del_dia_y_horario` | `string (text)` — Desayuno, Almuerzo, Té, Cena, Otras |
| `lugar_comida_habitual` | `enum: Casa \| Trabajo \| Otro` |
| `modifica_alimentacion_finde` | `string (text)` |
| `quien_compra_y_cocina` | `string (text)` |
| `horas_sueno_laboral` | `float (hours)` |
| `horas_sueno_festivo` | `float (hours)` |

**Pull — Preferencias y hábitos alimenticios**
| Field | Type |
|-------|------|
| `alimentos_que_no_consume` | `string (text)` |
| `alimentos_favoritos` | `string (text)` |
| `tipo_aceite_grasa` | `string (text)` |
| `metodo_coccion_habitual` | `string (text)` — plancha, vapor, fritos, etc. |
| `come_entre_horas` | `string (text)` |
| `frecuencia_comida_rapida` | `string (text)` |
| `tipo_azucar` | `enum: Azúcar \| Panela \| Edulcorantes \| Otros \| Ninguno` |
| `retira_piel_pollo` | `boolean` |

**Pull — Hidratación y suplementos**
| Field | Type |
|-------|------|
| `bebidas_durante_comidas` | `string (text)` |
| `bebidas_entre_comidas` | `string (text)` |
| `vasos_agua_dia` | `integer` |
| `bebidas_alcoholicas` | `string (text)` |
| `consume_suplementos` | `string (text)` — vitaminas, proteínas, colágeno, otros |

**Pull — Frecuencias de consumo** *(Si/No o cantidad)*
| Field | Type |
|-------|------|
| `4_cucharadas_aceite_dia` | `boolean` |
| `raciones_verdura_dia` | `integer` |
| `raciones_carne_roja_dia` | `integer` |
| `raciones_mantequilla_dia` | `integer` |
| `bebidas_carbonatadas_dia` | `integer` |
| `raciones_legumbres_semana` | `integer` |
| `raciones_pescado_mariscos_semana` | `integer` |
| `raciones_reposteria_semana` | `integer` |
| `raciones_frutos_secos_semana` | `integer` |
| `prefiere_carne_ave` | `boolean` |
| `acostumbra_sopas` | `boolean` |

**Pull — Historia de peso y actividad física**
| Field | Type |
|-------|------|
| `intentos_bajar_peso` | `boolean` |
| `acudio_profesional_peso` | `string (text)` |
| `dietas_realizadas` | `string (text)` |
| `momento_mas_hambre` | `string (text)` |
| `practica_deporte` | `string (text)` |
| `habito_caminar` | `string (text)` |

---

### Paso 3 — Opciones desde el perfil del paciente

#### Opción A — Subir recursos de paciente

| CTAs | Push | Pull |
|------|------|------|
| Subir recursos | Nuevos recursos | — |
| Save nuevos datos | Nuevos datos del paciente | — |

**Push — Nuevos recursos**
| Field | Type |
|-------|------|
| `patient_id` | `string (UUID)` |
| `resource_type` | `string` — plan nutricional, análisis, documento, etc. |
| `file_url` | `string (URL)` |
| `uploaded_by` | `string (UUID — admin_id)` |
| `uploaded_at` | `datetime` |

**Push — Nuevos datos del paciente**

Mismo esquema que el Pull del perfil completo. Solo se envían los campos que fueron modificados.

| Field | Type |
|-------|------|
| `patient_id` | `string (UUID)` |
| `updated_fields` | `object — subset of patient profile fields` |
| `updated_by` | `string (UUID — admin_id)` |
| `updated_at` | `datetime` |

---

#### Opción B — Observar datos

| CTAs | Push | Pull |
|------|------|------|
| — | — | Perfil completo del paciente |

> Pull trae el mismo conjunto de datos descrito en el Paso 2 de este flujo.

---

#### Opción C — Actualizar datos

| CTAs | Push | Pull |
|------|------|------|
| Save | Datos actualizados del paciente | — |

**Push — Datos actualizados**
| Field | Type |
|-------|------|
| `patient_id` | `string (UUID)` |
| `updated_fields` | `object — subset of patient profile fields` |
| `updated_by` | `string (UUID — admin_id)` |
| `updated_at` | `datetime` |

---

## Flujo 4: REELS

### Paso 1 — REELS

| CTAs | Push | Pull |
|------|------|------|
| Crear nueva categoría | — | Categorías |
| Crear nuevo reel | — | Reels |
| Editar o borrar reel | — | — |

**Pull — Categorías**
| Field | Type |
|-------|------|
| `category_id` | `string (UUID)` |
| `name` | `string` |
| `created_at` | `datetime` |

**Pull — Reels**
| Field | Type |
|-------|------|
| `reel_id` | `string (UUID)` |
| `title` | `string` |
| `category_id` | `string (UUID)` |
| `file_url` | `string (URL)` |
| `published_at` | `datetime` |

---

### Paso 2a — Editar o borrar reel

→ Abrir categoría del reel deseado → Elegir reel deseado

| CTAs | Push | Pull |
|------|------|------|
| Borrar | Reel eliminado | Reels (de la categoría seleccionada) |
| Editar | Reel actualizado | — |

**Pull — Reels (de la categoría seleccionada)**
| Field | Type |
|-------|------|
| `reel_id` | `string (UUID)` |
| `title` | `string` |
| `file_url` | `string (URL)` |
| `category_id` | `string (UUID)` |

**Push — Reel eliminado**
| Field | Type |
|-------|------|
| `reel_id` | `string (UUID)` |
| `deleted_by` | `string (UUID — admin_id)` |
| `deleted_at` | `datetime` |

**Push — Reel actualizado**
| Field | Type |
|-------|------|
| `reel_id` | `string (UUID)` |
| `title` | `string` |
| `file_url` | `string (URL)` |
| `updated_by` | `string (UUID — admin_id)` |
| `updated_at` | `datetime` |

---

### Paso 2b — Crear nuevo reel

→ Subir reel → Publica ahora (sistema lo guarda)

| CTAs | Push | Pull |
|------|------|------|
| Publicar | Nuevo reel | — |

**Push — Nuevo reel**
| Field | Type |
|-------|------|
| `reel_id` | `string (UUID — generated)` |
| `title` | `string` |
| `category_id` | `string (UUID)` |
| `file_url` | `string (URL)` |
| `created_by` | `string (UUID — admin_id)` |
| `published_at` | `datetime` |

---

### Paso 2c — Crear nueva categoría

→ Nombrar nueva categoría → Publicar

| CTAs | Push | Pull |
|------|------|------|
| Publicar | Nueva categoría | — |

**Push — Nueva categoría**
| Field | Type |
|-------|------|
| `category_id` | `string (UUID — generated)` |
| `name` | `string` |
| `created_by` | `string (UUID — admin_id)` |
| `created_at` | `datetime` |
