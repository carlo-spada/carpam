# Carpintería Huayapam - ERP+CRM+CMS Platform

Sistema integrado para digitalizar operaciones de carpintería artesanal, construido con el framework BMAD (Business Method and Design).

## Estado del Proyecto

**Track:** BMad Method (Greenfield)
**Fase Actual:** Fase 0 - Discovery
**Última Actualización:** 14 de noviembre, 2025

### Progreso

- ✅ Brainstorming completado (14-nov-2025)
- → Próximo paso: Research workflow

## Visión del Proyecto

Plataforma cloud-first con PWAs para usuarios internos y sitio web público para captación de clientes. La entidad central es el "Contrato de Entrada a Producción" (Orden de Trabajo) con 16 campos obligatorios.

### Objetivos Principales

El sistema debe ser:
- **Invisible en adopción** - Más simple que WhatsApp
- **Productivo inmediatamente** - No debe sentirse como carga administrativa
- **Grafo de conocimiento vivo** - No 3 módulos separados, sino datos interconectados

### Top 3 Prioridades

1. **Estructura de Orden de Trabajo** (16 campos obligatorios)
2. **Tablero Kanban de producción**
3. **Flujo unificado**: Cliente → Cotización → Orden de Trabajo

## Arquitectura Técnica (Planificada)

- **Enfoque:** Cloud-first con PWAs
- **PWAs específicas** por tipo de usuario
- **Sitio web público** para adquisición de clientes y marketing de contenidos
- **Componentes compartidos** en monorepo
- **Actualizaciones en tiempo real** para tablero Kanban

### Investigación Pendiente

1. Stack cloud-first (Next.js/SvelteKit + Supabase/Firebase)
2. Arquitectura multi-tenant
3. Patrones PWA para UX mobile-first
4. Auth/RBAC para múltiples roles
5. SSR/SSG para sitio público vs SPA para PWAs internas

## Estructura del Contrato de Entrada a Producción

### Datos del Cliente (4 campos)
1. Nombre del cliente
2. Teléfono/WhatsApp
3. Dirección de instalación
4. Canal de origen

### Especificaciones del Producto (6 campos)
5. Tipo de mueble
6. Medidas acordadas
7. Diseño básico
8. Material principal
9. Acabado
10. Condiciones especiales

### Plan de Materiales (2 campos)
11. Lista estimada de materiales
12. Verificación de disponibilidad

### Plan de Trabajo (4 campos)
13. Dueño del proyecto (carpintero)
14. Etapas y orden
15. Timeline y fecha objetivo
16. Precio acordado y términos de pago

## Framework BMAD

Este proyecto utiliza BMAD v6.0.0-alpha.9, que incluye:

- **4 módulos:** Core, BMM, CIS, BMB
- **16 agentes especializados:** Analyst, PM, Architect, SM, DEV, TEA, UX Designer, Tech Writer, y más
- **48 workflows:** Cubriendo el ciclo completo de desarrollo

### Comandos Principales

```bash
# Verificar estado actual
/bmad:bmm:workflows:workflow-status

# Próximo paso recomendado
/bmad:bmm:workflows:research

# Activar agentes
/bmad:bmm:agents:analyst
/bmad:bmm:agents:architect
```

## Documentación

- **Sesión de Brainstorming:** [`docs/bmm-brainstorming-session-2025-11-14.md`](docs/bmm-brainstorming-session-2025-11-14.md)
- **Estado de Workflows:** [`docs/bmm-workflow-status.yaml`](docs/bmm-workflow-status.yaml)
- **Guía BMAD:** [`bmad/bmm/docs/README.md`](bmad/bmm/docs/README.md)
- **Índice de Documentos:** [`docs/index.md`](docs/index.md)

## Próximos Pasos

1. **Research Workflow** - Investigación técnica del stack cloud-first
2. **Product Brief** - Definir visión, KPIs y roadmap
3. **PRD** - Requisitos detallados para MVP
4. **Arquitectura** - Decisiones técnicas colaborativas
5. **Sprint Planning** - Desglose en historias implementables

## Licencia

Proyecto privado - Carpintería Huayapam

---

**Desarrollado con:** BMAD Framework v6.0.0-alpha.9
**Repositorio:** https://github.com/carlo-spada/carpam
