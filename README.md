# Sistema de gestión de turnos para consultorios ambulatorios

Proyecto final de **Programación II** de la **UTN Facultad Regional Mar del Plata** (2026), realizado en equipo por:

- **Matías Lizaur**
- Gastón Medina
- Gerónimo Goncalves

> Este repositorio es una copia del [repositorio original del equipo](https://github.com/Gaston-M92/tpfinalprogra2), con el historial completo de commits de los tres integrantes.

## Descripción

Sistema por consola para administrar los turnos de un consultorio ambulatorio. Permite registrar pacientes, consultar los horarios libres de cada especialidad, asignar turnos validando la disponibilidad del médico y del paciente, cancelarlos y obtener un reporte estadístico. Toda la información se guarda en una base de datos MySQL.

### Funcionalidades

- Registro y listado de pacientes y médicos.
- Búsqueda de horarios libres por especialidad y fecha.
- Asignación de turnos con validaciones:
  - que el paciente exista,
  - que el médico atienda ese día y en ese horario,
  - que ni el médico ni el paciente tengan otro turno superpuesto.
- Cancelación de turnos (solo los que están reservados).
- Consulta de la agenda de un médico y de los turnos de un paciente.
- Reporte estadístico de turnos por estado.
- Persistencia en MySQL: los datos se conservan al cerrar y volver a abrir el programa.

## Tecnologías

- **Java 21**
- **Maven**
- **MySQL** con **JDBC** (MySQL Connector/J)
- **Git y GitHub** para el trabajo colaborativo

## Conceptos aplicados

| Concepto | Dónde se aplica |
|---|---|
| Herencia | `Paciente` y `Medico` heredan de `Persona`; las excepciones heredan de `Exception` |
| Polimorfismo | `generarFicha()`: abstracto en `Persona` e implementado distinto en cada subclase |
| Abstracción | Clase abstracta `Persona`; interfaces `Identificable`, `Reportable` y `DAO<T>` |
| Encapsulamiento | Atributos privados con getters y setters; constructor privado en `ConexionBD` |
| Clase genérica | `Gestor<T extends Identificable>`, que administra pacientes, médicos y turnos |
| Colecciones | `ArrayList` en `Gestor<T>`, en los horarios de cada médico y en los listados |
| Excepciones propias | `TurnoSuperpuestoException`, `HorarioNoDisponibleException`, `PacienteNoEncontradoException`, `TurnoNoCancelableException` |
| Persistencia | Patrón DAO con JDBC: `PacienteDAO`, `MedicoDAO` y `TurnoDAO` |
| Ordenamiento | `Paciente` y `Turno` implementan `Comparable` |

## Estructura del proyecto

```
├── docs/                  Diagrama UML
├── sql/turnos_db.sql      Script de creación de la base con datos de prueba
├── pom.xml                Configuración de Maven (driver de MySQL)
└── src/main/
    ├── java/
    │   ├── Main.java      Menú por consola
    │   ├── modelo/        Persona, Paciente, Medico, Turno, HorarioAtencion, Consultorio
    │   ├── gestor/        GestorTurnos (lógica del sistema) y Gestor<T> (clase genérica)
    │   ├── dao/           ConexionBD, PacienteDAO, MedicoDAO, TurnoDAO
    │   ├── Interfaces/    Identificable, Reportable, DAO<T>
    │   ├── Exceptions/    Excepciones propias del sistema
    │   └── Enums/         Especialidad, EstadoTurno
    └── resources/
        └── db.properties.example   Plantilla de configuración de la base
```

## Cómo ejecutarlo

1. **Crear la base de datos:** abrir `sql/turnos_db.sql` en MySQL Workbench y ejecutarlo. Crea la base `turnos_db` con pacientes, médicos y turnos de prueba.
2. **Configurar la conexión:** en `src/main/resources/`, copiar `db.properties.example` con el nombre `db.properties` y completar la contraseña de MySQL. Este archivo está en el `.gitignore` y no se sube al repositorio.
   ```
   db.url=jdbc:mysql://localhost:3306/turnos_db
   db.usuario=root
   db.clave=TU_CLAVE
   ```
3. **Abrir el proyecto** en IntelliJ IDEA desde la carpeta raíz (la que contiene `pom.xml`) y cargarlo como proyecto Maven.
4. **Ejecutar** `src/main/java/Main.java`.

Los horarios de atención de los médicos y los consultorios se generan en memoria al iniciar el programa; pacientes, médicos y turnos se leen de la base.

## Diagrama UML

El diagrama de clases está en [`docs/Diagrama_UML_Turnos_Proyecto_Final.pdf`](docs/Diagrama_UML_Turnos_Proyecto_Final.pdf).

## Posibles mejoras

- Validar que los turnos se asignen y cancelen solo en fechas futuras.
- Agregar opciones para marcar turnos como atendidos o ausentes desde el menú.
- Mostrar los horarios libres indicando el médico, para elegirlos directamente de una lista.
- Interfaz gráfica con JavaFX.

## Mi participación

Me encargué principalmente de la **capa de persistencia** y de **integrar el sistema con la base de datos**:

- **Base de datos:** diseño de las tablas `pacientes`, `medicos` y `turnos` (claves primarias, foráneas y restricciones `UNIQUE`) y script de creación con datos de prueba.
- **Conexión con JDBC:** clase `ConexionBD`, con los datos de conexión en un archivo `db.properties` excluido del repositorio para no exponer credenciales.
- **Patrón DAO:** interfaz genérica `DAO<T>` y las clases `PacienteDAO`, `MedicoDAO` y `TurnoDAO`, con operaciones CRUD usando `PreparedStatement` y `try-with-resources`. `TurnoDAO` reconstruye el paciente y el médico de cada turno a partir de sus DNI.
- **Integración del gestor con la base:** método `cargarDatos()`, que carga todo desde MySQL al iniciar, y guardado de pacientes y turnos al registrarlos, asignarlos o cancelarlos.
- **Correcciones en la lógica de turnos:** un error que colgaba la búsqueda de horarios libres, la validación para que un paciente no tenga dos turnos superpuestos y los mensajes de error.
- **Organización del proyecto:** estructura en paquetes, configuración de Maven y limpieza del repositorio.
- **Diagrama UML** final.

### Aportes del resto del equipo

- **Gastón Medina:** modelo de clases, clase genérica `Gestor<T>` y la lógica principal de `GestorTurnos`.
- **Gerónimo Goncalves:** excepciones propias, la clase `Turno` y el menú por consola.
