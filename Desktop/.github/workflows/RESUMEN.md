# DevOps y GitHub Actions vs Bitbucket Pipelines

## ¿Qué es DevOps?

DevOps es una cultura y conjunto de prácticas que une a los equipos de **Desarrollo (Dev)** y **Operaciones (Ops)** con el objetivo de entregar software de forma más rápida, frecuente y confiable.

### Principios clave

- **Integración Continua (CI):** Los desarrolladores integran su código frecuentemente en una rama compartida. Cada integración se verifica con builds y tests automatizados.
- **Entrega/Despliegue Continuo (CD):** El código validado se despliega automáticamente a entornos de prueba o producción.
- **Automatización:** Reducir tareas manuales repetitivas (tests, builds, despliegues) mediante pipelines.
- **Feedback rápido:** Detectar errores cuanto antes en el ciclo de desarrollo, no en producción.
- **Colaboración:** Romper los silos entre equipos de desarrollo, QA e infraestructura.

### El flujo DevOps típico

```
Código → Commit → Pipeline (CI) → Tests → Build → Deploy (CD) → Monitoreo
```

---

## GitHub Actions

GitHub Actions es la plataforma de automatización nativa de GitHub. Permite definir **workflows** (flujos de trabajo) en archivos YAML dentro de `.github/workflows/`.

### Conceptos fundamentales

| Concepto | Descripción |
|----------|-------------|
| **Workflow** | Proceso automatizado definido en un archivo `.yml` |
| **Event (on)** | Disparador del workflow (push, pull_request, schedule, etc.) |
| **Job** | Conjunto de pasos que se ejecutan en un runner |
| **Step** | Tarea individual dentro de un job (comando o action) |
| **Action** | Componente reutilizable publicado en el Marketplace |
| **Runner** | Servidor donde se ejecuta el job (`ubuntu-latest`, `windows-latest`, etc.) |

### Ejemplo de este proyecto

```yaml
# .github/workflows/ci.yml
name: CI Pipeline
on:
  push:
    branches: [main]
jobs:
  ci:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v4
        with:
          python-version: '3.10'
      - run: echo "Linter de IA ejecutado"
```

---

## GitHub Actions vs Bitbucket Pipelines

Ambas plataformas cumplen el mismo rol: automatizar CI/CD. La diferencia principal es el ecosistema al que pertenecen.

### Comparativa directa

| Característica | GitHub Actions | Bitbucket Pipelines |
|----------------|---------------|---------------------|
| **Plataforma** | GitHub (Microsoft) | Bitbucket (Atlassian) |
| **Archivo de config** | `.github/workflows/*.yml` | `bitbucket-pipelines.yml` (raíz del repo) |
| **Disparadores** | `on: push / pull_request / schedule / workflow_dispatch / ...` | `push / pull-requests / custom / scheduled` |
| **Unidad de trabajo** | Jobs con Steps | Steps dentro de Pipelines |
| **Reutilización** | Actions del Marketplace (miles disponibles) | Pipes del Marketplace (menor ecosistema) |
| **Runners** | GitHub-hosted o self-hosted | Atlassian Cloud o self-hosted |
| **Minutos gratuitos** | 2,000 min/mes (plan Free) | 50 min/mes (plan Free) |
| **Integración nativa** | GitHub PRs, Issues, Packages, Codespaces | Jira, Confluence, Trello |
| **Popularidad** | Mayor comunidad y documentación | Muy usada en entornos Atlassian |

### Equivalencia de sintaxis

**Bitbucket Pipelines:**
```yaml
# bitbucket-pipelines.yml
pipelines:
  branches:
    main:
      - step:
          name: Build y Test
          image: python:3.10
          script:
            - pip install -r requirements.txt
            - python -m pytest
```

**GitHub Actions equivalente:**
```yaml
# .github/workflows/ci.yml
name: CI Pipeline
on:
  push:
    branches: [main]
jobs:
  build-y-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v4
        with:
          python-version: '3.10'
      - run: pip install -r requirements.txt
      - run: python -m pytest
```

### Principales diferencias de migración

1. **Un solo archivo vs múltiples archivos:** Bitbucket usa un único `bitbucket-pipelines.yml`; GitHub Actions permite separar workflows en múltiples archivos dentro de `.github/workflows/`.

2. **`image` vs `runs-on`:** En Bitbucket se especifica la imagen Docker directamente en cada step. En GitHub Actions, `runs-on` define el runner base y las Actions manejan el entorno.

3. **`pipes` vs `uses`:** Las integraciones de terceros en Bitbucket se llaman *Pipes*; en GitHub Actions se llaman *Actions* y se referencian con `uses: owner/repo@version`.

4. **Variables de entorno:** Bitbucket usa `$BITBUCKET_*` como variables reservadas. GitHub Actions usa `$GITHUB_*` (ej: `$GITHUB_SHA`, `$GITHUB_REF`).

---

## Resumen

Si ya conoces Bitbucket Pipelines, GitHub Actions es conceptualmente idéntico: defines eventos, jobs y pasos en YAML. La migración es directa mapeando `image` → `runs-on`/`setup-*`, `script` → `run`, y `pipes` → `uses`. La ventaja de GitHub Actions está en su ecosistema más grande, mejor integración con el resto de GitHub, y mayor generosidad en minutos gratuitos.
