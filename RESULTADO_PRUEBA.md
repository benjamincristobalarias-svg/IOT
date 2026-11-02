# Resultado de la prueba: pantalla de Registro (registro.patch)

Rama: `claude/prueba-registro-iot-9o0keg`, creada desde `main` (6228b67).
Probado en una sesión de Claude Code en la nube (Linux, JDK 21), **no** en un PC con Android Studio.

## 1. Aplicar el patch

`git am registro.patch` → se aplicó sin conflictos (commit 35f247a). No hubo que corregir nada.

## 2. `gradlew testDebugUnitTest` y `gradlew assembleDebug`

**No se pudieron ejecutar.** La red de este entorno bloquea `dl.google.com`
(`CONNECT tunnel failed, response 403`), que es de donde se descargan el Android SDK,
el Android Gradle Plugin, androidx y Firebase. Tampoco había `google-services.json`.

En su lugar, las pruebas unitarias (que solo usan Kotlin puro) se corrieron en un proyecto
Gradle JVM aparte (Kotlin 2.3.21, kotlinx-coroutines, JUnit 4.13.2) con los mismos archivos:
`model/*.kt`, `data/EstructurasRepository.kt`, `data/FakeEstructurasRepository.kt` y `src/test`.

| Clase | Pruebas | Fallas |
|---|---|---|
| LoginYActivadorTest | 10 | 0 |
| SimulacionTest | 5 | 0 |
| ExampleUnitTest | 1 | 0 |

Las 4 pruebas nuevas del registro pasaron: `registro_datosCorrectos_sinError`,
`registro_contrasenasDistintas_muestraError`, `registro_nombreVacio_muestraError`,
`registro_contrasenaCorta_muestraError`.

Las pantallas (Compose), `AuthRepository` (Firebase) y el Manifest **no se compilaron**.
Se revisaron a mano: imports, firmas y llamadas entre `MainActivity`, `LoginScreen`,
`RegistroScreen`, `LoginViewModel` y `AuthRepository` coinciden. Queda pendiente
correr `gradlew assembleDebug` en Android Studio.

## 3. Cambios para corregir errores

Ninguno.

## 4. Pruebas en el celular / emulador

No se hicieron (no hay emulador ni SDK en este entorno). Pendientes en el PC:

| Prueba | Esperado | Resultado |
|---|---|---|
| Registro con contraseñas distintas | "Las contraseñas no coinciden" | Pendiente |
| Registro correcto | Entra y dice "Hola, *nombre*" | Pendiente |
| Cerrar sesión y volver a entrar | Vuelve al Login y entra de nuevo | Pendiente |
| Activador en un celular y Monitoreo en el otro | Los datos llegan en vivo | Pendiente |
