# StockGuard 🚀

Sistema automatizado para la gestión y clasificación de inventarios, desarrollado como proyecto práctico para la asignatura de Big Data. El proyecto cuenta con una arquitectura modular en Python, pruebas unitarias automatizadas y un flujo de Integración Continua (CI) a través de GitHub Actions.

## 📂 Estructura del Proyecto

```text
github-actions-stockguard/
├── .github/
│   └── workflows/
│       └── ci.yml             # Configuración de GitHub Actions (CI)
├── src/
│   ├── __init__.py
│   └── inventario.py          # Lógica principal de clasificación de stock
├── tests/
│   └── test_inventario.py     # Pruebas unitarias con Pytest
├── README.md                  # Documentación y respuestas de análisis
└── requirements.txt           # Dependencias del sistema (pytest)

🛠️ Desarrollo e Hitos Alcanzados
Inicialización del Entorno: Configuración inicial del repositorio local Git en la rama principal main.

Vinculación Remota: Creación del repositorio en la nube y enlace con GitHub (origin).

Casos de Prueba: Cobertura de código mediante 5 pruebas unitarias locales orientadas a evaluar los diferentes estados del inventario (Agotado, Crítico, Bajo, Normal, Inválido).

Automatización: Configuración exitosa de un pipeline de Integración Continua que valida la sintaxis y ejecuta las pruebas en un contenedor Linux limpio ante cada cambio del código.

📝 Bitácora de Errores y Soluciones (Troubleshooting)
Durante el despliegue del proyecto en entornos locales y remotos se solventaron los siguientes inconvenientes técnicos:

Error de Rutas en Local (ModuleNotFoundError: No module named 'src')

Problema: Al ejecutar el comando directo pytest -v en la terminal, Python no lograba reconocer la carpeta raíz del proyecto y fallaba al importar los módulos desde src.

Solución: Se cambió el método de ejecución al modo módulo de Python (python -m pytest -v), lo que obliga al intérprete a inyectar el directorio de trabajo actual directamente en el sys.path.

Falla de Localización en Servidor de CI (Entorno Linux)

Problema: El contenedor automatizado de GitHub Actions fallaba en la ejecución de las pruebas por la misma razón: invocaba a Pytest sin el contexto de la ruta raíz.

Solución: Se migró el paso de testing en el archivo ci.yml a la instrucción estandarizada python -m pytest -v, logrando así una ejecución exitosa (PASSED).

📝 Respuestas a las Preguntas de Análisis
1. Identifica una ejecución exitosa y una fallida. ¿Qué commit o cambio produjo cada una y qué step determinó el resultado?

Ejecución fallida: Fue producida por el commit 2511313 (push inicial a main). El step que determinó el fallo fue "Ejecutar pruebas con pytest", que arrojó un ModuleNotFoundError.

Ejecución exitosa: Fue producida por el commit 0d3e1e4 al corregir el archivo YAML. El step determinante fue el mismo (ejecutar pytest), pero esta vez terminó en verde porque las 5 pruebas pasaron correctamente.

2. ¿Por qué ejecutar pytest localmente no reemplaza ejecutar pytest en GitHub Actions?
Porque mi entorno local (Windows) tiene configuraciones, versiones de Python y variables de entorno distintas al servidor de GitHub (Linux). Ejecutarlo en Actions garantiza que el código funcionará en un entorno limpio y estandarizado, evitando el clásico "en mi máquina sí funciona".

3. ¿Qué riesgo tendría hacer merge a main sin esperar los checks del Pull Request?
El riesgo es subir código que rompa la rama principal (main). Si no esperamos a que Actions valide las pruebas, podríamos integrar errores de sintaxis o lógica que afecten el trabajo de los demás y dañen la versión estable del proyecto.

4. En tu ejecución fallida, copia solo la línea esencial del error y explica qué te permitió diagnosticar.

Línea del error: E ModuleNotFoundError: No module named 'src'

Diagnóstico: Me permitió identificar que el problema no era la lógica de las pruebas (no era un AssertionError), sino un problema de rutas de importación. El servidor no sabía dónde buscar la carpeta src al ejecutar el comando.

5. ¿Qué diferencia existe entre un step que usa uses: y uno que utiliza run:?

uses: se utiliza para llamar a una acción preconstruida y reutilizable (por ejemplo, actions/checkout@v4 para descargar el código o actions/setup-python@v5 para instalar Python).

run: se utiliza para ejecutar comandos de consola o scripts directamente en la máquina virtual (por ejemplo, pip install -r requirements.txt o python -m pytest -v).

6. ¿Qué información aporta ${{ github.actor }} en run-name y por qué puede ser útil en un proyecto en equipo?
Aporta el nombre de usuario de GitHub que disparó la ejecución (por ejemplo, "CI ejecutado por RichardChachalo"). Es útil en equipo para saber rápidamente quién hizo el último push o quién inició el flujo manual, facilitando el seguimiento y la asignación de responsabilidades.

7. Si tu workflow pasa pero la aplicación completa falla localmente con Docker, ¿significa que Actions “no sirve”?
No. Significa que Actions está verificando las condiciones mínimas de calidad (sintaxis de Python, pruebas unitarias de la lógica de inventario, configuración), pero no está levantando toda la infraestructura (Kafka, Spark, Airflow, Docker). Actions valida que el código base esté sano, pero no reemplaza las pruebas de integración completas.

8. Propón tres checks para tu variante de RapidGo. Para cada uno explica qué error concreto evitaría.

Check de sintaxis Python (compileall): Evita subir archivos con errores de indentación o variables mal escritas que rompan la ejecución del script.

Check de validación de JSON: Evita que el productor de datos (Kafka) envíe mensajes con estructuras mal formadas que el consumidor no pueda leer.

Check de importación de DAGs de Airflow: Evita que el pipeline de orquestación falle porque un DAG tiene dependencias rotas o errores de sintaxis.

9. ¿Cuál de esos checks debería implementarse primero y por qué?
El de sintaxis Python (compileall), porque es el más rápido de ejecutar. No tiene sentido correr pruebas complejas o validar JSON si el código base ni siquiera puede ser leído por Python.

10. ¿Qué prueba sería demasiado costosa o frágil para ejecutar en cada push y cómo la validarías entonces?
Levantar todo el clúster de Kafka + Spark + Airflow en cada push. Sería demasiado costoso en tiempo (minutos de ejecución) y frágil (los contenedores pueden fallar por falta de recursos). Se validaría localmente antes de hacer el release final o mediante una ejecución manual programada (workflow_dispatch) en horarios de baja carga.
