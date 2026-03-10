# MicroSpringBoot

Prototipo mínimo de un servidor web en Java que usa reflexión para descubrir componentes anotados con `@RestController`, mapear métodos anotados con `@GetMapping` y resolver parámetros anotados con `@RequestParam`.

## Requisitos implementados

- Servidor HTTP básico con `ServerSocket`.
- Atención de múltiples solicitudes no concurrentes.
- Descubrimiento automático de controladores dentro del paquete base.
- Soporte para respuestas dinámicas `String`.
- Soporte para páginas HTML y recursos estáticos `.png` desde `src/main/resources/static`.
- Aplicación de ejemplo con `/hello`, `/pi`, `/greeting` e `index.html`.

## Ejecutar

Compilar y probar:

```bash
mvn test
```

Levantar el servidor en el puerto por defecto `8080`:

```bash
mvn compile
java -cp target/classes com.arep.microspringboot.MicroSpringBoot
```

Levantar el servidor indicando paquete base y puerto:

```bash
java -cp target/classes com.arep.microspringboot.MicroSpringBoot com.arep.microspringboot 8080
```

También es posible cargar un único controlador por nombre completo:

```bash
java -cp target/classes com.arep.microspringboot.MicroSpringBoot com.arep.microspringboot.HelloController 8080
```

## Endpoints de ejemplo

- `/`
- `/hello`
- `/pi`
- `/greeting?name=AREP`
- `/index.html`
- `/images/pixel.png`

## Evidencia AWS

La evidencia de despliegue en AWS no puede generarse automáticamente desde este entorno. Para completar el entregable final, despliegue el `jar` en una instancia EC2 o una VM equivalente, ejecute el servidor y adjunte captura de pantalla o logs con una petición exitosa al puerto publicado.