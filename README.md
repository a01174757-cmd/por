# BEERLab — Experimento de percepción de categorías raciales (oTree)

Versión `0.4.1` · Python 3.11 · oTree 6.0.15

## 1. ¿Qué mide el estudio?

Cada participante califica fotografías de rostros con una pregunta:

> ¿Consideras a esta persona **[CATEGORÍA]**?

La respuesta se da con un control deslizante de **0 (definitivamente no)** a **1 (definitivamente sí)**, **en pasos de 0.1** (0, 0.1, 0.2 … 1). El servidor rechaza cualquier otro valor. No hay botón de confirmar: basta mover la barra y pulsar **Siguiente**. Por seguridad, las pantallas eliminan cualquier botón «Confirmar este valor» que una plantilla externa pudiera agregar.

Cada participante recibe **una sola categoría** durante todo el estudio, asignada al azar entre cuatro condiciones:

| condicion_id | Categoría que ve el participante |
|---|---|
| 1 | negra(o) |
| 2 | afromexicana(o) |
| 3 | afrodescendiente |
| 4 | afromexicana(o), negra(o) o afrodescendiente (frase del INEGI) |

## 2. Así funciona el experimento

**Pantallas, en orden:** bienvenida → consentimiento (18 años o más) → datos generales → instrucciones → rostro de práctica → **10 rostros** → cierre.

1. **Cada participante ve 10 rostros.**
2. **Hay un rostro por cada nivel** del continuo, del 1 (más caucásico) al 10 (más fenotípicamente negro).
3. **Siempre son 5 hombres y 5 mujeres**, en orden aleatorio.
4. **El banco tiene 20 rostros: un hombre y una mujer por nivel.** Los participantes se asignan **por parejas, en el orden en que aceptan el consentimiento**:
   - La persona 1 recibe una categoría y 5 niveles al azar en los que ve al hombre.
   - **La persona 2 recibe la misma categoría y la lista espejo**: ve a la mujer donde la 1 vio al hombre, y al revés.
   - Las personas 3 y 4 forman la siguiente pareja, y así sucesivamente.
   - Las categorías de las parejas rotan en bloques de 4 (una pareja por categoría, en orden aleatorio): **cada 8 personas hay 2 de cada categoría**.

   Así, **cada rostro lo ve exactamente la mitad de cada pareja**, y las categorías quedan casi iguales aunque no se sepa cuántas personas van a responder.
5. **Todos ven el mismo rostro de práctica (CFD-IF-734-108)** antes de empezar. No forma parte del banco y su respuesta no aparece en los resultados.
6. **Las fotos son las originales del Chicago Face Database (CFD 3.0.1)**, expresión neutral, en alta calidad (1400 × 984 px) y sin recortes.

**La asignación se hace al aceptar el consentimiento**, no al crear la sesión. Quien abre el enlace y se va antes de aceptar no afecta el balance. Si alguien acepta y abandona a medias, su pareja queda sin complemento; se identifica con `orden_llegada`, `lista_balanceo` y `lista_espejo`. Cada sesión empieza su propio conteo de parejas.

## 3. Banco de rostros

| rostro_id | Archivo | ID CFD | Nivel | Sexo |
|---|---|---|---|---|
| 1 | rostro_01.jpg | LM-251 | 1 | Hombre |
| 2 | rostro_02.jpg | WF-203 | 1 | Mujer |
| 3 | rostro_03.jpg | LM-236 | 2 | Hombre |
| 4 | rostro_04.jpg | LF-237 | 2 | Mujer |
| 5 | rostro_05.jpg | LM-206 | 3 | Hombre |
| 6 | rostro_06.jpg | LF-246 | 3 | Mujer |
| 7 | rostro_07.jpg | LM-207 | 4 | Hombre |
| 8 | rostro_08.jpg | LF-254 | 4 | Mujer |
| 9 | rostro_09.jpg | LM-247 | 5 | Hombre |
| 10 | rostro_10.jpg | LF-209 | 5 | Mujer |
| 11 | rostro_11.jpg | MM-301 | 6 | Hombre |
| 12 | rostro_12.jpg | LF-241 | 6 | Mujer |
| 13 | rostro_13.jpg | MM-303 | 7 | Hombre |
| 14 | rostro_14.jpg | MF-335 | 7 | Mujer |
| 15 | rostro_15.jpg | BM-031 | 8 | Hombre |
| 16 | rostro_16.jpg | BF-209 | 8 | Mujer |
| 17 | rostro_17.jpg | BM-012 | 9 | Hombre |
| 18 | rostro_18.jpg | BF-207 | 9 | Mujer |
| 19 | rostro_19.jpg | BM-224 | 10 | Hombre |
| 20 | rostro_20.jpg | BF-240 | 10 | Mujer |
| práctica | practica.jpg | IF-734-108 | — | Mujer |

La misma tabla está en el código como `ROSTROS`, en `percepcion/__init__.py`.

## 4. Estructura del proyecto

```
BEERLab_otree_azul_blanco/
├── settings.py          configuraciones de sesión (sin contraseñas ni base de datos)
├── requirements.txt     versiones exactas
├── README.md
├── .gitignore
├── percepcion/
│   ├── __init__.py      tabla de rostros, asignación, páginas y exportación
│   ├── tests.py         bots de prueba
│   └── *.html           las 7 pantallas
└── _static/rostros/     rostro_01.jpg … rostro_20.jpg y practica.jpg
```

## 5. Cómo correrlo y probarlo

Requiere **Python 3.11** (requisito del BEERLab). Dependencias en `requirements.txt`: `otree==6.0.15`, `psycopg2-binary==2.9.10` y `requests==2.32.3` (oTree la usa para correr los bots).

```bash
python3.11 -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
python -m pip install -r requirements.txt
export SECRET_KEY="$(python -c 'import secrets; print(secrets.token_hex(32))')"
export ADMIN_PASSWORD="$(python -c 'import secrets; print(secrets.token_urlsafe(16))')"
otree resetdb
otree test
otree devserver 8000
```

**Configuraciones de sesión:**

| Nombre | Uso |
|---|---|
| `percepcion` | **Producción.** Reparte las 4 condiciones al azar y en partes iguales. |
| `piloto_negro`, `piloto_afromexicano`, `piloto_afrodescendiente`, `piloto_inegi` | Solo para pruebas. Cada una fija una condición. **No usar para reclutamiento.** |

No hay Rooms (`ROOMS = []`). Se puede usar **el enlace único de la sesión** (*session-wide link*) y mandarlo a todas las personas para que respondan desde casa. Crea la sesión con **más lugares de los que esperas** (por ejemplo, 40 para 30 personas): cuando se acaban los lugares, el enlace deja de admitir gente. Los lugares que no se usen no afectan el balance.

**En Codespaces:** abre el puerto 8000 desde la pestaña *Puertos* (dirección `.app.github.dev`). Si oTree genera enlaces con `localhost`, conserva la ruta y cambia el inicio por la dirección de Codespaces.

## 6. Datos y exportación

**En la tabla de datos de oTree (Data / SessionData y `all_apps_wide`)**, cada rostro tiene su propia columna con su código del CFD: `respuesta_LM_251`, `respuesta_WF_203` … `respuesta_BF_240` (con guion bajo en lugar de guion). **La calificación ya aparece en la columna del rostro**, sin importar en qué orden lo vio el participante. Cada participante llena solo las 10 columnas de los rostros que vio; las otras 10 quedan vacías. El orden de presentación está en `orden_rostros` (lista de códigos CFD).

En el panel administrativo de oTree, entra a **Data** y descarga el **CSV personalizado de la app `percepcion`**. Es el archivo para el análisis: **cada fila es un rostro evaluado** (10 filas por participante).

| Columna | Contenido |
|---|---|
| `participante_codigo` | Código seudónimo del participante (`participant.code`) |
| `sesion_codigo` | Código de la sesión |
| `condicion_id`, `condicion_texto` | Categoría asignada (1–4 y su texto) |
| `orden_llegada` | Orden en que la persona aceptó el consentimiento (1, 2, 3…) |
| `lista_balanceo`, `lista_espejo` | Número de pareja (1 = llegadas 1 y 2, 2 = llegadas 3 y 4…) y si recibió la lista espejo |
| `orden_presentacion` | Posición del rostro (1–10) |
| `rostro_cfd` | ID del rostro en el Chicago Face Database |
| `archivo_imagen` | Archivo de la foto |
| `nivel_asignado` | Nivel del continuo (1–10) |
| `sexo_rostro` | Hombre o Mujer (permite filtrar por sexo del rostro) |
| `respuesta_0_a_1` | Calificación del participante |
| `edad`, `genero`, `estado_nacimiento`, `escolaridad` | Datos generales del participante (el estado de nacimiento se elige de una lista con los 32 estados y «Nací fuera de México») |

- **Solo se exportan participantes que completaron los 10 rostros.** Los incompletos quedan en los datos brutos de oTree (`all_apps_wide`).
- **La práctica no se exporta.**
- Exporta también `all_apps_wide` y, si se necesitan tiempos de respuesta, `PageTimes`.
- **Exporta después de cada sesión.** Las copias nocturnas del laboratorio son solo para recuperación.

**Protección de datos:** no se piden nombres ni correos. Los códigos de participante son seudónimos. Los datos se guardan solo en la base de datos del laboratorio (nada se escribe en el disco del servidor), y el acceso se limita al equipo autorizado y al docente responsable, según el consentimiento aprobado.

## 7. Variables de entorno

| Variable | Quién la configura |
|---|---|
| `ADMIN_PASSWORD` | Laboratorio |
| `SECRET_KEY` | Laboratorio |
| `DATABASE_URL` | Laboratorio (nunca aparece en `settings.py`) |

No se usan variables `OTREE_` propias.

## 8. Ficha técnica para el BEERLab

| Dato | Valor |
|---|---|
| Versión | `0.4.1` (agregar tag/commit de Git o nombre y fecha del ZIP) |
| Subdominio propuesto | `percepcion` (sujeto a disponibilidad) |
| Configuración de producción | `percepcion` |
| Configuraciones de piloto | las cuatro `piloto_*` |
| Fechas, número de sesiones y participantes simultáneos | **Por definir con el laboratorio** |
| Persona autorizada para el acceso administrativo | **Por definir por el equipo** |
| Pagos | No hay pagos |
| Archivos que suben los participantes | Ninguno |

El proyecto no incluye `Procfile`, `runtime.txt` ni Dockerfile.

## 9. Pruebas realizadas

Con **Python 3.11 y oTree 6.0.15** (23 de noviembre de 2026):

- `otree test` termina con **`Bots completed session`** en `percepcion` y en las cuatro configuraciones `piloto_*`.
- Sesión simulada de **60 participantes**:
  - Entre 14 y 16 por condición (la categoría se rota por parejas de llegada).
  - Todos con **un rostro por nivel y 5 hombres / 5 mujeres**.
  - Cada lista complementa exactamente a su pareja.
  - Cada rostro recibió 7 u 8 calificaciones por condición.
  - El CSV personalizado tiene **600 filas** (10 por participante).
- Sesión de 30 bots: cada pareja por orden de llegada tiene la misma categoría y listas espejo; cada rostro se vio exactamente 15 veces.
- Simulación de abandonos (30 aceptan, 4 abandonan a medias): las 4 categorías quedan entre 5 y 8 personas y cada rostro entre 2 y 4 calificaciones por categoría; solo se rompen las parejas de quienes abandonaron.
- Recorrido completo en `otree devserver`, en escritorio y celular.
- El servidor rechaza calificaciones que no sean múltiplos de 0.1 (por ejemplo, 0.55).
- En los datos de los 60 participantes simulados, cada calificación quedó en la columna `respuesta_<código>` del rostro correcto.

## 10. Pendiente antes de reclutar

- [ ] Recorrido manual completo por alguien del equipo.
- [ ] Validar el texto del consentimiento contra el expediente aprobado (agregar contacto del responsable).
- [ ] Generar tag/commit de Git o nombrar el ZIP con versión y fecha.
- [ ] Definir con el BEERLab fechas, capacidad simultánea, subdominio, persona responsable y envío seguro de credenciales.
- [ ] No incluir datos de prueba ni contraseñas en la entrega.

## Colores

Azul principal `#003DA5` · fondo `#F7F9FD`.
