# Bot Mesa de Ayuda IT — TPI Organización Empresarial

**Tecnicatura Universitaria en Programación a Distancia (TUPaD)**  
Cátedra: Organización Empresarial | Prof. Gabriela Martínez  

---

## Descripción del proyecto

Simulador de chatbot web para la **Mesa de Ayuda IT del Ministerio de Seguridad Provincial** (organización ficticia). El bot automatiza el proceso de soporte técnico nivel 1, desde la recepción de una consulta hasta su resolución o escalado, siguiendo un modelo de procesos **BPMN 2.0**.

**Proceso automatizado:** Soporte Técnico Nivel 1 (Mesa de Ayuda IT)

---

## Estructura del repositorio

```
/
├── chatbot_mesa_ayuda_it.html   # Aplicación principal (todo-en-uno)
├── bpmn_mesa_ayuda_it.svg       # Diagrama BPMN 2.0 exportado
├── README.md                    # Este archivo
└── docs/
    └── TPI_informe.pdf          # Informe entregable (4-8 páginas)
```

---

## Cómo ejecutar

### Opción 1 — Abrir directamente (recomendado)
1. Descargar `chatbot_mesa_ayuda_it.html`
2. Hacer doble clic en el archivo → se abre en el navegador
3. No requiere servidor, conexión a internet ni instalaciones adicionales

### Opción 2 — Servidor local (Python)
```bash
# Python 3
python -m http.server 8080
# Luego abrir: http://localhost:8080/chatbot_mesa_ayuda_it.html
```

### Opción 3 — Visual Studio Code
Instalar la extensión **Live Server** → clic derecho en el `.html` → *Open with Live Server*

---

## Cómo usar el chatbot

| Paso | Acción |
|------|--------|
| 1 | El bot saluda y solicita el **número de legajo** |
| 2 | Ingresar un legajo válido: `2341`, `1892` o `3105` |
| 3 | Seleccionar la **categoría** del problema con los botones |
| 4 | Describir brevemente el problema en el campo de texto |
| 5 | El bot consulta la **KB** y entrega solución o escala |
| 6 | Confirmar si el problema fue resuelto |

**Para reiniciar:** escribir `reiniciar` en cualquier momento.

---

## Flujo BPMN implementado

```
[Inicio] → Usuario accede → Bot saluda y pide legajo
         → ¿Legajo válido? ─┬─ NO → Error (máx. 3 intentos) → Fin
                            └─ SI → Menú de categorías
         → ¿Categoría válida? ─┬─ NO → Pide reingresar (máx. 3 intentos)
                               └─ SI → Pide descripción
         → Consulta KB ─┬─ SOLUCIÓN ENCONTRADA → Envía solución
                        └─ SIN SOLUCIÓN → Escala a técnico
         → ¿Resuelto? ─┬─ SI → Cierra ticket → [Fin]
                       └─ NO → Escala a técnico → [Fin]
```

---

## Base de datos simulada

El sistema simula una base de datos con dos tablas:

### `BD_USUARIOS` (3 registros)
| Legajo | Nombre | Sector | Email |
|--------|--------|--------|-------|
| 2341 | García, Roberto | Guardia | rgarcia@minseg.gob.ar |
| 1892 | López, Andrea | Administración | alopez@minseg.gob.ar |
| 3105 | Martínez, Carlos | Sistemas | cmartinez@minseg.gob.ar |

### `BD_TICKETS` (generados en tiempo de ejecución)
```json
{
  "id": "IT-20250617-4821",
  "estado": "Cerrado",
  "usuario": { "legajo": "2341", "nombre": "García, Roberto" },
  "categoria": "🌐 Sin conexión a red",
  "descripcion": "No puedo acceder a internet desde mi PC",
  "solucion": "1) Verificar cable...",
  "prioridad": "Media",
  "fecha": "17/06/2025, 10:35:00"
}
```

### `BD_KB` (Knowledge Base — 5 categorías)
| Clave | Categoría | Prioridad |
|-------|-----------|-----------|
| red | Sin conexión a red | Media |
| impresora | Problema con impresora | Baja |
| contraseña | Restablecimiento de contraseña | Alta |
| sistema | Sistema lento/cuelga | Media |
| otro | Consulta general | Baja |

> La BD simulada puede visualizarse en tiempo real desde el panel "Base de datos simulada" al pie del chatbot.

---

## Máquina de estados

```
SALUDO → ESPERANDO_LEGAJO → ESPERANDO_CATEGORIA → ESPERANDO_DESCRIPCION
       → CONFIRMANDO_TICKET → MOSTRANDO_SOLUCION → ESPERANDO_CONFIRMACION
       → CERRADO | ESCALADO
```

Cada estado se muestra en el indicador BPMN al pie de la interfaz.

---

## Manejo de errores (Camino Infeliz)

| Error | Comportamiento |
|-------|---------------|
| Legajo inválido | Informa el intento (máx. 3), luego cierra y deriva al interno 210 |
| Categoría no reconocida | Repite opciones (máx. 3 intentos), luego escala |
| Descripción muy corta (< 5 chars) | Solicita más detalle antes de continuar |
| Sin solución en KB | Escala automáticamente a técnico nivel 2 |
| Sesión cerrada + nuevo input | Informa el estado y sugiere reiniciar |

---

## Stack tecnológico

| Componente | Tecnología | Justificación |
|------------|-----------|---------------|
| Plataforma | Web (HTML5) | Acceso universal, sin instalación |
| Frontend | HTML + CSS + JS vanilla | Sin dependencias, corre offline |
| Lógica de negocio | JavaScript ES6+ | Máquina de estados, manejo de errores |
| Persistencia | Objeto JS en memoria (BD simulada) | Simula MySQL/SQLite sin servidor |
| Modelado | BPMN 2.0 | Estándar industrial para procesos |
| IA utilizada | Claude (Anthropic) | Asistencia en diseño y código |

### Lenguajes alternativos posibles en producción real
- **Backend:** PHP 8.x + MySQL (stack actual del Ministerio)
- **Bot real:** Python + `python-telegram-bot` o WhatsApp Business API
- **Base de datos:** MySQL con tablas `tickets`, `usuarios`, `kb`

---

## Arquitectura BPMN 2.0

El diagrama incluye:
- **2 carriles (Lanes):** Usuario / Sistema-Bot
- **Eventos:** Inicio (círculo verde) y Fin (círculo rojo doble)
- **3 compuertas (Gateways):** ¿Legajo válido?, ¿Categoría válida?, ¿Resuelto?
- **Tareas de usuario:** Acceder, describir problema, confirmar resolución
- **Tareas de sistema:** Validar, registrar, consultar KB, escalar, cerrar

---

## Herramientas de IA utilizadas

**Claude (Anthropic) — claude.ai**

Se utilizó para:
1. Diseño del diagrama BPMN 2.0 y validación de la notación
2. Arquitectura de la máquina de estados en JavaScript
3. Generación del HTML/CSS/JS del simulador
4. Revisión del diccionario de datos y manual de usuario

**Prompts clave utilizados:**
- *"Diseñá un diagrama BPMN 2.0 para un chatbot de mesa de ayuda IT con carriles Usuario y Sistema, incluyendo compuertas de decisión y caminos alternativos"*
- *"Implementá una máquina de estados en JavaScript para el flujo del bot, con manejo del camino infeliz (legajo inválido, categoría no reconocida, sin solución KB)"*
- *"Generá el HTML/CSS del chatbot simulando una BD con tabla de tickets, usuarios y KB, visible en tiempo real"*

---

## Autor

**Andres MANRIQUEZ** — Tecnicatura Universitaria en Programación a Distancia  
Organización Empresarial — 2026

---

## Licencia

Trabajo académico — uso educativo. Prohibida la reproducción comercial.
