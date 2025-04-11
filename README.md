# 📚 Documentación de QBasic

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![MkDocs Material](https://img.shields.io/badge/MkDocs-Material-informational)](https://squidfunk.github.io/mkdocs-material/)

Repositorio de documentación exhaustiva sobre **QBasic**, dirigida a:

- **Estudiantes universitarios** que aprenden fundamentos de programación.
- **Desarrolladores** que trabajaron con QBasic en los 90s.
- **Equipos de migración** que modernizan aplicaciones legacy.
- **Entusiastas** que desean aprender y crear software retro.

## 🚀 Características

✔️ **Cobertura técnica completa**: Desde sintaxis básica hasta gráficos 3D y gestión de memoria.  
✔️ **Enfoque práctico**: Ejemplos de código listos para usar.  
✔️ **Organizada con MkDocs**: Navegación intuitiva y búsqueda integrada.  

## 📦 Estructura del Repositorio

```bash
QBASIC-DOCS/
├── docs/                      # Documentación en Markdown
│   ├── 2D.md                  # Gráficos 2D (SCREEN 13, sprites)
│   ├── 3D.md                  # Técnicas de pseudo-3D y raycasting
│   ├── implementaciones.md    # Migración a lenguajes modernos
│   └── ...                    # +20 archivos organizados por temas
├── pdfs/                      # Versiones PDF de la documentación
├── mkdocs.yml                 # Configuración del sitio web
└── README.md                  # Este archivo
```

## 🌐 Cómo Usar Esta Documentación

### Opción 1: Navegar Online

Visita el sitio web generado con MkDocs.

### Opción 2: Ejecutar Localmente

1. Clona el repositorio:

   ```bash
   git clone https://github.com/tu-usuario/QBASIC-DOCS.git
   ```

2. Instala MkDocs y dependencias:

   ```bash
   pip install mkdocs mkdocs-material
   ```

3. Previsualiza la documentación:

   ```bash
   mkdocs serve
   ```

   Abre `http://localhost:8000` en tu navegador.

## 🛠️ Contribuciones

¡Se aceptan contribuciones! Si tienes:

- 📝 Correcciones técnicas.
- 💡 Ejemplos de código avanzado.

Sigue estos pasos:

1. Haz un fork del proyecto.
2. Crea una rama con tu contribución (`git checkout -b feat/nueva-seccion`).
3. Envía un Pull Request.

## 📄 Licencia

Este proyecto está bajo la licencia **MIT**. Ver [LICENSE](LICENSE) para más detalles.

---

## 🔍 ¿Por Qué QBasic en 2024?

### 🏗️ Contexto del Proyecto

Esta documentación surgió al migrar un sistema crítico desde:

- **Hardware original**: IBM 486 con MS-DOS.
- **Entorno intermedio**: DOSBox (emulación).
- **Destino**: Plataforma moderna (Angular/Python/Java).

Durante el proceso, recolectamos y unificamos conocimiento disperso:  
✔️ Notas técnicas en papel.  
✔️ El manual original del sistema fisico en papel (muy desgastado).  
✔️ Documentacion original de Microsoft en PDFs.  
✔️ Técnicas de optimización ya olvidadas, en diferentes sitios web.  

> *"Decidimos que este conocimiento debía sobrevivir más allá de nuestro proyecto."*

### 🎯 Valor Actual

- **Para migraciones**: Guía técnica detallada para entender lógicas legacy.  
- **Como legado**: Preserva técnicas históricas relevantes en computación.  

## 🌍 Soporte de Idiomas (Language Support)

**Español (Actual)** | **English (Planned)**  
Esta documentación se mantiene actualmente **únicamente en español**, ya que es el idioma principal de los contribuidores y el público objetivo inicial. Sin embargo, reconocemos la importancia de una versión en inglés para alcance global.

**Planes futuros**:  

- [ ] Traducción completa al inglés.  
- [ ] Soporte para alternar entre idiomas en el sitio web.  
- [ ] Documentación paralela en `/docs/es/` y `/docs/en/`.  

**Why only Spanish for now?**  

- Focus on quality: Preferimos profundizar en contenido técnico preciso antes de escalar a otros idiomas.  
- Community-driven: Si deseas colaborar con traducciones, ¡abre un *issue* o *PR*!  

---

> *"La documentación técnica debe ser primero correcta, luego multilingüe."* — Principio del proyecto.

## ⚠️ Nota sobre el Proceso de Documentación Inicial (Procesamiento con IA 🦾)

**Metodología de Documentación**:  
Esta documentación fue creada mediante un proceso semi-automatizado utilizando el modelo de lenguaje **Claude 3.7 Sonnet** (de Anthropic) para organizar y estructurar documentos originales a esta documentacion.  

**Se advierte que**:  

- Puede contener **errores de interpretación** en secciones técnicas complejas (verificar con fuentes primarias).  
- Los ejemplos de código requieren **validación manual** (especialmente en migraciones críticas) y deben probarse en entornos controlados.  
- Referencias a hardware específico (ej: puertos COM) deben verificarse contra sistemas reales.  
- Priorizamos utilidad práctica sobre perfección en esta fase.  

> *Las actualizaciones continuarán según hallazgos durante la migración.*  
