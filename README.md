# MicroSpringBoot

Prototipo mínimo de un servidor web en Java que usa reflexión para descubrir componentes anotados con `@RestController`, mapear métodos anotados con `@GetMapping` y resolver parámetros anotados con `@RequestParam`.

## Requisitos implementados

- Servidor HTTP básico con `ServerSocket`.
- Atención de múltiples solicitudes no concurrentes.
- Descubrimiento automático de controladores dentro del paquete base.
- Soporte para respuestas dinámicas `String`.
- Soporte para páginas HTML y recursos estáticos `.png` desde `src/main/resources/static`.
- Aplicación de ejemplo con `/hello`, `/pi`, `/greeting`, `/images/pixel.png` e `index.html`.

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

## Endpoints de ejemplo

- `/`
- `/hello`
- `/pi`
- `/greeting?name=AREP`
- `/index.html`
- `/images/pixel.png`

## Evidencia AWS

- `mvn package`

![](/src/main/resources/static/evidences/1.png)

- `java -cp target/classes com.arep.microspringboot.MicroSpringBoot`

![](/src/main/resources/static/evidences/2.png)

### Test endpoints

![](/src/main/resources/static/evidences/2.png)

![](/src/main/resources/static/evidences/3.png)

![](/src/main/resources/static/evidences/4.png)

![](/src/main/resources/static/evidences/5.png)

![](/src/main/resources/static/evidences/6.png)

![](/src/main/resources/static/evidences/7.png)

![](/src/main/resources/static/evidences/8.png)