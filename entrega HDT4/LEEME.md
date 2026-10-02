# HDT 4 — entrega de Bruno - Victor Saravia Carné no. 20240060


Este ZIP contiene las colecciones `bruno/smoke/` y `bruno/regresion/`, sus reportes JUnit y la tabla `tecnicas.md`.

Las colecciones usan el formato OpenCollection YAML que genera Bruno actual. Por eso cada raíz contiene `opencollection.yml` en lugar de `bruno.json`. Ambas incluyen `environments/local.yml` con `baseUrl=http://127.0.0.1:8000`.

Para repetir las pruebas, inicia el API con `uv run api.py` desde el repositorio del ejercicio. Después, desde `bruno/smoke/`, ejecuta:

```bash
bru run --env local --reporter-junit junit-smoke.xml
```

Desde `bruno/regresion/`, ejecuta:

```bash
bru run --env local --reporter-junit junit-regresion.xml
```

Los reportes incluidos fueron generados con Bruno CLI 4.2.0: smoke, 5 de 5; regresión, 15 de 15. Las colecciones también pasaron una corrida previa completa cada una.
