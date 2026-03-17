# MicroSpringBoot

Prototipo mínimo de un servidor web HTTP en Java puro que usa reflexión para descubrir componentes anotados con `@RestController`, mapear métodos anotados con `@GetMapping` y resolver parámetros anotados con `@RequestParam`.

El servidor es **concurrente** (atiende múltiples peticiones en paralelo mediante un pool de hilos) y soporta **apagado elegante** (graceful shutdown) al registrar un JVM shutdown hook que espera a que los hilos activos terminen antes de cerrar.

---

## Resumen del proyecto

| Aspecto | Detalle |
|---------|---------|
| Lenguaje | Java 17 |
| Build | Maven |
| Tests | JUnit 5 |
| Concurrencia | `ExecutorService` con pool de 10 hilos |
| Apagado elegante | JVM shutdown hook (espera hasta 30 s) |
| Contenedor | Docker (imagen `eclipse-temurin:17-jre-alpine`) |

---

## Arquitectura

```
┌──────────────────────────────────────────────────────────────────┐
│                         MicroSpringBoot                          │
│                                                                  │
│  main()                                                          │
│   ├─ resolveConfiguration()  ← args (puerto / paquete base)      │
│   ├─ loadControllers()       ← escanea @RestController en CP     │
│   └─ startServer()                                               │
│        ├─ ServerSocket.accept()  (bucle principal)               │
│        ├─ ExecutorService (pool 10 hilos)                        │
│        │    └─ handleClient(Socket)  ← por cada petición         │
│        │         └─ handleRequest(method, target)                │
│        │              ├─ RouteDefinition lookup                  │
│        │              │    └─ invokeRoute() via reflexión        │
│        │              └─ loadStaticResource() desde classpath    │
│        └─ Shutdown Hook                                          │
│             ├─ serverSocket.close()                              │
│             └─ executor.awaitTermination(30 s)                   │
└──────────────────────────────────────────────────────────────────┘
```

---

## Diseño de clases

```
MicroSpringBoot                          (clase principal)
 ├─ Configuration                        (datos de configuración del servidor)
 │    basePackage, controllerClassName, port
 ├─ RouteDefinition                      (ruta registrada)
 │    controllerInstance, method, parameters
 ├─ ParameterDefinition                  (parámetro de un método de ruta)
 │    name, defaultValue
 └─ HttpResponse                         (respuesta HTTP encapsulada)
      statusCode, statusText, contentType, body

Anotaciones
 ├─ @RestController   (nivel de clase)   → marca un controlador
 ├─ @GetMapping       (nivel de método)  → declara una ruta GET
 └─ @RequestParam     (nivel de param.)  → liga un parámetro de query string

Controladores de ejemplo
 ├─ HelloController      → GET /,  GET /hello,  GET /pi
 └─ GreetingController   → GET /greeting?name=...
```

---

## Requisitos implementados

- Servidor HTTP básico con `ServerSocket`.
- **Atención concurrente** de múltiples solicitudes mediante `ExecutorService` (pool de 10 hilos).
- **Apagado elegante** (graceful shutdown) vía JVM shutdown hook.
- Descubrimiento automático de controladores dentro del paquete base mediante reflexión.
- Soporte para respuestas dinámicas `String`.
- Soporte para páginas HTML y recursos estáticos `.png` desde `src/main/resources/static`.
- Aplicación de ejemplo con `/hello`, `/pi`, `/greeting`, `/images/pixel.png` e `index.html`.

---

## Ejecutar localmente

Compilar y probar:

```bash
mvn test
```

Levantar el servidor en el puerto por defecto `8080`:

```bash
mvn package -DskipTests
java -jar target/microspringboot-1.0-SNAPSHOT.jar
```

Apagar con `Ctrl+C` (dispara el shutdown hook).

---

## Generar imagen Docker y desplegar

### Prerrequisitos
- Docker ≥ 20 instalado.

### Construir la imagen

```bash
docker build -t microspringboot:latest .
```

### Ejecutar el contenedor

```bash
docker run -d -p 8080:8080 --name microspringboot microspringboot:latest
```

### Usar Docker Compose (recomendado)

```bash
docker compose up -d          # iniciar en segundo plano
docker compose down           # detener y eliminar el contenedor
```

### Publicar en Docker Hub (opcional, para despliegue en EC2)

```bash
docker tag microspringboot:latest <usuario>/microspringboot:latest
docker push <usuario>/microspringboot:latest
```

---

## Despliegue en AWS EC2

1. Lanzar una instancia EC2 (Amazon Linux 2 / Ubuntu) y abrir el puerto **8080** en el Security Group.
2. Conectarse vía SSH y ejecutar:

```bash
# Instalar Docker
sudo yum update -y           # Amazon Linux
sudo yum install -y docker
sudo service docker start
sudo usermod -aG docker ec2-user
# (reconectar SSH para que surta efecto el grupo)

# Descargar la imagen y levantar el servicio
docker pull <usuario>/microspringboot:latest
docker run -d -p 8080:8080 --restart unless-stopped \
  --name microspringboot <usuario>/microspringboot:latest
```

3. Verificar en el navegador: `http://<IP-publica-EC2>:8080`

---

## Endpoints de ejemplo

| Método | Ruta | Respuesta |
|--------|------|-----------|
| GET | `/` | Página HTML de inicio |
| GET | `/hello` | `Hello World!` |
| GET | `/pi` | `PI= 3.141592653589793` |
| GET | `/greeting?name=AREP` | `Hola AREP` |
| GET | `/index.html` | Página HTML estática |
| GET | `/images/pixel.png` | Imagen PNG |

---

## Evidencia AWS

![Video Evidencias AWS](src/main/resources/static/evidences/evidencias_aXB33r8d.mp4)
