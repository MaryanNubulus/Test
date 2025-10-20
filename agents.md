# Guía para Desarrollo

## Guía de Desarrollo Frontend (React + TypeScript)

### I. Configuración y Stack Tecnológico

| Requisito           | Herramientas/Conceptos                | Justificación según Fuentes                                                                                                      |
| ------------------- | ------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| **Framework Base**  | React + Componentes Funcionales       | Los componentes funcionales son el estándar de facto, reemplazando a las clases, y enfatizan la simplicidad y la componibilidad. |
| **Tipado**          | TypeScript (TS)                       | Seguridad de tipos, detecta errores en compilación, mejora la DX y sirve como documentación implícita.                           |
| **Build Tool**      | Vite                                  | Tiempos de inicio casi instantáneos y Hot Module Replacement rápido, acelerando el desarrollo.                                   |
| **Linting/Calidad** | ESLint + Configuración estricta de TS | Habilita reglas type-aware y fortalece contratos detectando errores temprano.                                                    |

---

### II. Arquitectura de Componentes y Patrones

#### A. Modularidad y Responsabilidad Única

1. **Componentes Pequeños**: Funcionales y con responsabilidad única para facilitar pruebas y reutilización.
2. **Hooks Personalizados (Custom Hooks)**: Extraer lógica de estado reutilizable para separar preocupaciones y evitar duplicación.
3. **Patrones**: Container/Presentational, efectos bien encapsulados para reducir acoplamiento y mejorar legibilidad.

#### B. TypeScript Avanzado en Componentes

1. **Props Type-Safe**: Interfaces bien tipadas y validadas en compilación.
2. **Hooks Type-Safe**: Consistencia y autocompletado usando TS en `useState`, `useReducer` y custom hooks.
3. **Componentes Genéricos**: `<T>` para crear componentes reutilizables para cualquier tipo de dato con seguridad estricta.

#### C. Renderización (React 19 y RSC)

1. **Server Components (RSC)**: Para tareas de acceso a datos de servidor antes de enviar HTML al cliente. No incrementan el bundle de JS y no usan hooks de estado.
2. **Client Components**: Marcados con `"use client"`, permiten estado, efectos y event handlers, contribuyendo al tamaño del bundle.

---

### III. Gestión de Estado y Datos

| Tipo de Estado                  | Práctica Recomendada         | Descripción                                                                                         |
| ------------------------------- | ---------------------------- | --------------------------------------------------------------------------------------------------- |
| **Estado Remoto**               | React Query (TanStack Query) | Maneja cache, revalidación y estados de carga de datos de servidor, centralizando el estado remoto. |
| **Estado Local/UI**             | `useState` o `useReducer`    | Para estados efímeros y específicos de la interfaz.                                                 |
| **Estado Global de Aplicación** | Context API + Nuevo `use()`  | Para temas, autenticación, localización y estados amplios.                                          |
| **Optimización UI**             | `useOptimistic` (React 19)   | Updates optimistas para mostrar estado inmediatamente antes de respuesta de red.                    |

---

### IV. Calidad, Testing y Accesibilidad

#### A. Estrategia de Testing Moderna

1. **Unitarias y de Integración**: Jest + Testing Library para tests centrados en comportamiento observable.
2. **E2E (Extremo a Extremo)**: Verifica flujos completos y regresiones visuales, integradas en pipelines CI.
3. **Nomenclatura**: Método/Módulo_Condición_ResultadoEsperado.
   - Ejemplo: `Post_TodoItem_ReturnsCreatedItem`.

#### B. Rendimiento y Optimización

1. **Memorización Selectiva**: React.memo, `useMemo` y `useCallback` tras perfilar con React DevTools para evitar renders innecesarios.

#### C. Accesibilidad

1. Integración temprana desde el diseño.
2. Roles ARIA, etiquetas semánticas, control de foco y estados dinámicos anunciados por lectores de pantalla.
3. Tests de accesibilidad automatizados y manuales.

---

### V. Flujo de Trabajo y Estilado

#### A. Estilado (CSS)

1. **Utility-First CSS**: Tailwind CSS para componer clases directamente en JSX, rápido y consistente.
2. **Componentes Empresariales**: Librerías como KendoReact para componentes complejos y accesibles.
3. **Estructura Modular Alternativa**: CSS puro modular (`root.css`, `global.css`, archivos por componente) con variables CSS (`--primary`, `--spacing-md`, `clamp()`).

#### B. Integración y Procesos DevOps

1. **Gestión de Trabajo**: Work Items o User Stories en Backlog, completadas en Sprints.
2. **Flujo de Código**: Cambios por Repository, revisados mediante Pull Requests antes de fusión.
3. **Automatización CI/CD**: Pipeline de pasos automatizados para construir, probar y desplegar.

## Guía de Desarrollo Backend (C# .NET API REST - Feature Based)

### I. Arquitectura de Proyecto: Basada en Features

La arquitectura principal será la **Vertical Slice Architecture (VSA)**, que divide la aplicación en "tallas verticales" (**Features**) que representan funcionalidades completas e independientes. Esto facilita el mantenimiento y la escalabilidad del código, ideal para la Agilidad.

Esta estructura se implementa a menudo dentro de los principios de **Clean Architecture** o **Onion Architecture**.

#### Estructura del Proyecto (Inspirada en DDD/Clean Architecture)

El proyecto se dividirá en **capas lógicas** para asegurar la separación de responsabilidades, con la carpeta `Features/` conteniendo la lógica central de la aplicación:

| Capa                                 | Responsabilidad Principal                                                                              | Ejemplos de Contenido                                                       |
| ------------------------------------ | ------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------- |
| **Domain (Core)**                    | Contiene las reglas del dominio y las entidades, independiente de cualquier tecnología externa.        | Entities, ValueObjects, Aggregates, Interfaces de Repositorios              |
| **Application**                      | Lógica de la aplicación y coordinación de tareas. Contiene la implementación de los casos de uso.      | DTOs, Servicios, UseCases (Comandos y Consultas)                            |
| **Infrastructure**                   | Implementación de las interfaces definidas en Domain y Application (persistencia, servicios externos). | Implementaciones de Repositorios (ej. Entity Framework), Servicios de Email |
| **Web (Presentation)**               | Capa de presentación (los Controllers de la API REST).                                                 | Controladores HTTP, Modelos de Presentación                                 |
| **Features (Dentro de Application)** | Estructura VSA: Cada carpeta representa una funcionalidad completa (ej., `Products/CreateProduct`).    | Commands, Queries, Handlers, Validators específicos de la funcionalidad     |

---

### II. Modelado de Dominio (DDD) y C#

El modelado de datos debe ser preciso, utilizando los conceptos de **Domain-Driven Design (DDD)**.

#### Tipos de Objetos

1. **Entity (Entidad)**: Objeto del dominio con identidad única y ciclo de vida (ej. `Customer`, `BankAccount`). Encapsula datos y comportamiento. Se utiliza en la Capa de Dominio.
2. **Value Object (Objeto de Valor)**: Objetos descriptivos sin identidad propia, definidos únicamente por sus valores (ej. `Address`, `Money`). Son inmutables y se comparan por valor.
3. **Aggregate (Agregado)**: Agrupación de Entidades y Value Objects que se tratan como una unidad para asegurar la consistencia. Tienen un **Aggregate Root** que es el único punto de entrada para modificar su estado.
4. **DTO (Data Transfer Object)**: Utilizado para transferir datos entre capas (ej. de la capa de Servicio a la de Presentación). No contiene lógica de negocio.

#### Eventos de Dominio y de Integración

- **Domain Event**: Representa algo que ha pasado dentro del dominio para notificar otras partes del mismo dominio. Se procesan **in-process**.

  - Nomenclatura: `[Acción]Event`
  - Ejemplo: `OrderCreatedEvent`

- **Integration Event**: Se publica para comunicarse con otros sistemas o servicios fuera del dominio (esencial en microservicios). Se procesan **out-of-process** y asíncronamente.

  - Nomenclatura: `[Acción]IntegrationEvent`
  - Ejemplo: `OrderCreatedIntegrationEvent`

- **Handlers**: Clases que manejan estos eventos. Deben terminar con `Handler`.
  - Ejemplo: `OrderCreatedEventHandler`

---

### III. API REST y Patrones de Endpoint

Dado que se trata de una API REST, los endpoints deben ser limpios, mantenibles y fáciles de testear.

#### Patrón REPR (Request-Endpoint-Response)

Se utilizará el patrón **REPR** dentro de la capa `Web/Controllers` para asegurar la separación de responsabilidades.

1. **Request (DTO de Entrada)**: Objeto que encapsula los datos de entrada.
2. **Endpoint (Controller/Action)**: Función del controlador que procesa la solicitud, utilizando los servicios de aplicación.
3. **Response (DTO de Salida)**: Objeto que encapsula el resultado o la salida que se devuelve al cliente.

**Beneficios:** El patrón REPR mejora la testabilidad y la mantenibilidad al aislar la lógica de manejo de cada solicitud.

---

### IV. Convenciones y Calidad de Código (C#)

Para garantizar la legibilidad y la coherencia en el código C#:

| Elemento                        | Convención de Nomenclatura               | Ejemplo                          |
| ------------------------------- | ---------------------------------------- | -------------------------------- |
| Clases / Proyectos / Constantes | PascalCase                               | ProductService, MyAwesomeProject |
| Funciones / Métodos             | PascalCase                               | CalculateTotalPrice()            |
| Variables Locales / Parámetros  | camelCase                                | quantity, unitPrice              |
| Interfaces                      | PascalCase y comienzan con "I" mayúscula | IProductRepository               |
| Enumeraciones (Enums)           | PascalCase para el nombre y los valores  | OrderStatus, Pending             |

---

### V. Flujo de Calidad, Testing y DevOps

Las prácticas de calidad deben integrarse para soportar la **velocidad de entrega continua (CI/CD).**

#### Testing Estratégico

1. **Unit Tests (Pruebas Unitarias)**:

   - Verifican la funcionalidad de componentes individuales.
   - Deben centrarse en la lógica del **Domain** y la **Application**.
   - Nomenclatura: clara y descriptiva, siguiendo: `MethodName_StateUnderTest_ExpectedBehavior`.
   - Ejemplo: `Suma_DeuIMesDeu_RetornaVint`

2. **Integration Tests (Pruebas de Integración)**:
   - Aseguran que los diferentes componentes (API, servicios, persistencia) funcionan correctamente juntos.
   - Son esenciales para la capa **Web** y la interacción con **Infrastructure**.
   - Nomenclatura: debe reflejar el módulo o funcionalidad y el resultado esperado.
   - Ejemplo: `Autenticacio_UsuariValid_RetornaToken`
   - Se recomienda el uso de **WebApplicationFactory** para simular peticiones HTTP a la API REST.

#### Integración Continua / Despliegue Continuo (CI/CD)

- **Repositorio (Repo)**: El código fuente debe guardarse en un espacio de almacenamiento con historial de cambios (ej. GitHub).
- **Control de Versiones**: Usar **Branches** para trabajar de manera aislada y **Commits** con mensajes descriptivos.
- **Pull Requests (PR)**: Toda fusión de código debe ser revisada (PR) antes de integrarse en la rama principal.
- **Pipeline**: Automatización de construcción, pruebas y despliegue. El **CI/CD** implica la integración continua de cambios y su despliegue automático en producción, esencial para la agilidad.

