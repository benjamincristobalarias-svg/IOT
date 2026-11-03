# Resultado de la prueba: pantalla de Registro (registro.patch)

Rama: `claude/prueba-registro-iot-9o0keg`, creada desde `main` (6228b67).
Probado en una sesión de Claude Code en la nube (Linux), **no** en un PC con Android Studio.

## Resumen

| Paso | Resultado |
|---|---|
| `git am registro.patch` | Se aplicó sin conflictos |
| `gradlew testDebugUnitTest` | **BUILD SUCCESSFUL**: 16 pruebas, 0 fallas |
| `gradlew assembleDebug` | **BUILD SUCCESSFUL**: `app-debug.apk` (14,9 MB) |
| Correcciones al código | Ninguna |
| Pruebas en celular / emulador | No se hicieron (pendientes) |

## 1. Entorno usado

- Android SDK instalado con `sdkmanager`: `platforms;android-37.0`, `build-tools;37.0.0`, `platform-tools`.
- Gradle 9.6.0 (el wrapper del repo), Android Gradle Plugin 9.4.1, Kotlin 2.3.21.
- Java 25: `gradle/gradle-daemon-jvm.properties` pide Java 25 y lo descarga de `api.foojay.io`,
  que estaba bloqueado en la nube. Se instaló `openjdk-25-jdk-headless` con apt. **En el PC no hace falta**:
  Android Studio lo descarga solo.
- `google-services.json` **de relleno** (paquete `cl.inacap.monitorestructural`, claves falsas), solo para
  que compile. No se subió (está en `.gitignore`). El APK generado no se conecta a Firebase.
- Maven Central respondió dos veces "429 Too Many Requests"; se reintentó y pasó. No es un problema del proyecto.

## 2. `gradlew testDebugUnitTest`

| Clase | Pruebas | Fallas |
|---|---|---|
| LoginYActivadorTest | 10 | 0 |
| SimulacionTest | 5 | 0 |
| ExampleUnitTest | 1 | 0 |

Las 4 pruebas nuevas del registro pasaron: `registro_datosCorrectos_sinError`,
`registro_contrasenasDistintas_muestraError`, `registro_nombreVacio_muestraError`,
`registro_contrasenaCorta_muestraError`.

Salida (sin las tareas UP-TO-DATE):

```text
> Task :app:generateDebugResources
> Task :app:processDebugGoogleServices
> Task :app:javaPreCompileDebugUnitTest
> Task :app:javaPreCompileDebug
> Task :app:mapDebugSourceSetPaths
> Task :app:packageDebugResources
> Task :app:createDebugCompatibleScreenManifests
> Task :app:extractDeepLinksDebug
> Task :app:processDebugNavigationResources
> Task :app:checkDebugAarMetadata
> Task :app:compileDebugNavigationResources
> Task :app:processDebugMainManifest
> Task :app:processDebugManifest
> Task :app:processDebugManifestForPackage
> Task :app:parseDebugLocalResources
> Task :app:generateDebugRFile
> Task :app:mergeDebugResources
> Task :app:processDebugResources
> Task :app:compileDebugKotlin
> Task :app:processDebugJavaRes
> Task :app:bundleDebugClassesToCompileJar
> Task :app:bundleDebugClassesToRuntimeJar
> Task :app:compileDebugUnitTestKotlin
> Task :app:processDebugUnitTestJavaRes
> Task :app:testDebugUnitTest
BUILD SUCCESSFUL in 46s
25 actionable tasks: 25 executed
```

## 3. `gradlew assembleDebug`

```text
> Task :app:mergeDebugAssets
> Task :app:compressDebugAssets
> Task :app:desugarDebugFileDependencies
> Task :app:mergeDebugJavaResource
> Task :app:checkDebugDuplicateClasses
> Task :app:mergeDebugJniLibFolders
> Task :app:generateDebugGlobalSynthetics
> Task :app:mergeDebugNativeLibs
> Task :app:mergeLibDexDebug
> Task :app:stripDebugDebugSymbols
Unable to strip the following libraries, packaging them as they are: libandroidx.graphics.path.so, libdatastore_shared_counter.so. Run with --info option to learn more.
> Task :app:writeDebugAppMetadata
> Task :app:writeDebugSigningConfigVersions
> Task :app:dexBuilderDebug
> Task :app:mergeProjectDexDebug
> Task :app:validateSigningDebug
> Task :app:mergeExtDexDebug
> Task :app:packageDebug
> Task :app:assembleDebug
> Task :app:createDebugApkListingFileRedirect
BUILD SUCCESSFUL in 1m 9s
37 actionable tasks: 18 executed, 19 up-to-date
```

El aviso "Unable to strip the following libraries" es normal en un build debug y no es un error.
No hubo advertencias del compilador de Kotlin.

### Revisión del Manifest final del APK

- `allowBackup=false`, `dataExtractionRules`, `fullBackupContent` y `networkSecurityConfig`: presentes.
- Permisos: `INTERNET`, `ACCESS_NETWORK_STATE` y además
  `com.google.android.providers.gsf.permission.READ_GSERVICES` y `DYNAMIC_RECEIVER_NOT_EXPORTED_PERMISSION`.
  Estos dos últimos los agregan las librerías de Firebase / AndroidX, no la app. Ojo: `docs/app/seguridad_y_pruebas.md`
  dice "Solo los permisos INTERNET y ACCESS_NETWORK_STATE"; conviene precisar que son los que pide la app.

## 4. Cambios para corregir errores

Ninguno. El patch compiló y pasó las pruebas tal como venía.

## 5. Pruebas en el celular / emulador

No se hicieron: la máquina en la nube no puede correr el emulador de Android (sin KVM) y no hay celular conectado.
Además el APK compilado aquí usa un `google-services.json` falso. Hay que hacerlas en Android Studio con el archivo real:

| Prueba | Esperado | Resultado |
|---|---|---|
| Registro con contraseñas distintas | "Las contraseñas no coinciden" | Pendiente |
| Registro correcto | Entra y dice "Hola, *nombre*" | Pendiente |
| Cerrar sesión y volver a entrar | Vuelve al Login y entra de nuevo | Pendiente |
| Activador en un celular y Monitoreo en el otro | Los datos llegan en vivo | Pendiente |
