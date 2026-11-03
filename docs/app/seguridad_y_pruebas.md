# Seguridad y pruebas de la app

## Medidas de seguridad (ISO/IEC 27001, Anexo A)

| Medida | Cómo está en la app | Control |
|---|---|---|
| Control de acceso | Login obligatorio y reglas `auth != null` | 5.15, 8.3 |
| Autenticación segura | Firebase Authentication; contraseña oculta; registro con nombre de usuario, mínimo 8 caracteres y confirmar contraseña; mismo mensaje si falla el correo o la contraseña | 8.5 |
| Credenciales protegidas | La app no guarda la contraseña; Firebase la guarda cifrada (hash) | 5.17 |
| Cifrado en tránsito | HTTPS/TLS entre la app y Firebase; `network_security_config.xml` bloquea cualquier conexión sin cifrar (HTTP) | 8.24 |
| Protección de datos en el celular | `allowBackup="false"` y reglas de extracción: la sesión no se copia a respaldos en la nube ni a otro celular | 8.10, 8.12 |
| Integridad de los datos | Reglas que validan tipos y rangos (0–100 Hz) y rechazan campos desconocidos | 8.26 |
| Registro de eventos | `historial` guarda cada lectura con el UID de quien la envió; no se puede modificar ni borrar desde la app | 8.15 |
| Mínimo privilegio | Solo los permisos INTERNET y ACCESS_NETWORK_STATE (sin Bluetooth ni ubicación) y solo dos ramas de la base; `google-services.json` fuera del repo | 8.3 |

## Lineamientos OT (base: IEC 62443)

- **Identificar usuarios:** login obligatorio.
- **Control de uso:** solo dos ramas; historial que no se modifica; la línea base no se cambia desde la app (se registra en terreno).
- **Integridad:** valores fuera de rango rechazados por las reglas.
- **Disponibilidad:** indicador "Conectado a Firebase / Sin conexión"; sin internet Firebase guarda los envíos y los manda al volver.
- **Conexión y condición separadas:** un sensor desconectado no se muestra como "Normal"; su lectura queda como "no actual".
- **Sin datos inventados:** si no hay lectura se guarda `null` y se muestra "Sin datos".

## Pruebas

- **15 pruebas automáticas** (`SimulacionTest.kt`, `LoginYActivadorTest.kt`): reglas de daño y sismo, simulador, login, registro (nombre vacío, contraseña corta, contraseñas distintas, datos correctos) y Activador. Pasaron todas.
- **6 pruebas de reglas** en la Zona de pruebas de reglas de Firebase: lectura sin sesión rechazada, lectura con sesión autorizada, 500 Hz rechazado, 30 Hz autorizado, historial a nombre de otro usuario rechazado, historial a nombre propio autorizado.
- **Pruebas con dos celulares** (WiFi, datos móviles, sin internet, envío automático, sensor desconectado, contraseña incorrecta): se muestran en la demostración en clase.

## Pendiente

- Reemplazar el Activador por el ESP32 enviando por WiFi (el ESP32 también tendrá que iniciar sesión).
- En producción: desactivar el registro libre y que el administrador cree las cuentas.
