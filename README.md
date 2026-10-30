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
