# Actividad de Exploración — Sistema de Gestión de Tutorías entre Pares, por Departamento, en Modalidad Híbrida

## 1. Conceptos importantes y relevantes en la temática

- **Rol dual del estudiante**: una misma entidad *Estudiante* puede participar como tutorado en unas materias y como tutor en otras, según su desempeño académico.
- **Requisitos para ser tutor**: condiciones que determinan si un estudiante puede habilitarse como tutor de una materia (nota mínima aprobada, semestre cursado, aval del departamento). Son datos que deben quedar modelados y validables.
- **Departamento/Facultad como unidad organizativa**: cada tutor, materia y aula pertenece a un departamento, lo que implica relaciones de pertenencia entre estas entidades.
- **Sesión de tutoría**: entidad central del modelo; puede ser individual o grupal, y tiene un cupo máximo de estudiantes cuando es grupal.
- **Modalidad de la sesión**: atributo que define si la sesión es presencial o virtual, y que condiciona qué otra entidad se relaciona con la sesión (un aula si es presencial, un enlace/recurso virtual si es virtual).
- **Aula/recurso físico**: entidad con capacidad y disponibilidad, que no debe asignarse dos veces al mismo horario (restricción de integridad).
- **Inscripción a sesión**: relación muchos a muchos entre *Estudiante* y *Sesión*, necesaria para representar sesiones grupales.
- **Evaluación del tutor**: datos asociados a una sesión que registran la calificación u observación que el tutorado hace sobre el tutor.
- **Historial de tutoría**: conjunto de registros históricos de sesiones por estudiante, usados para análisis y reportes, sin implicar ninguna lógica de aplicación, solo estructura de datos.

## 2. Tendencias actuales en dichos conceptos (a nivel de modelado de datos)

- **Modelado de roles duales mediante especialización/generalización**: en vez de crear tablas separadas y desconectadas para "Tutor" y "Estudiante", las bases de datos actuales modelan al tutor como una especialización del estudiante, evitando duplicidad de datos personales.
- **Normalización de la disponibilidad**: la disponibilidad de tutores y aulas se modela en tablas independientes de franjas horarias, permitiendo validar solapamientos mediante restricciones (constraints) o consultas, en lugar de campos de texto libre.
- **Modelado de la modalidad como atributo condicional**: la tendencia es representar la modalidad (presencial/virtual) como un atributo del registro de sesión que determina qué claves foráneas son obligatorias (aula vs. recurso virtual), en vez de tener tablas separadas por modalidad.
- **Tablas puente para relaciones muchos a muchos**: el manejo de sesiones grupales exige tablas intermedias (por ejemplo, "Inscripción") para representar correctamente la relación entre estudiantes y sesiones.
- **Trazabilidad histórica**: las bases de datos actuales conservan el historial completo de sesiones (no solo el estado actual), permitiendo generar reportes de uso por departamento, materia o tutor.
- **Integridad referencial y reglas de negocio en la base de datos**: cada vez más se busca que las reglas (cupo máximo, requisitos de tutor, no solapamiento) se validen a nivel de estructura de datos y restricciones, no solo en la lógica de una aplicación.

## 3. Análisis de herramientas existentes en el mercado (enfoque en su modelo de datos)

> Nota: el análisis se centra en qué entidades y relaciones maneja cada herramienta a nivel conceptual, no en su interfaz ni en su tecnología de desarrollo.

### Herramienta 1: Starfish (EAB)

Plataforma de éxito estudiantil que gestiona asesoría académica y agendamiento de tutorías.

**Entidades/relaciones que maneja (a nivel conceptual):**
- Estudiante, Asesor/Tutor, Cita, Modalidad de cita (presencial/virtual).
- Relaciona cada cita con seguimiento de riesgo académico y alertas del estudiante.

**Lo que aporta como referencia de modelado:** representar la modalidad como atributo de la cita, y vincular el historial de citas con indicadores de seguimiento del estudiante.
**Lo que no cubre:** no modela el rol dual estudiante/tutor ni requisitos de habilitación como tutor; su relación tutor-departamento no es un eje central del modelo.

### Herramienta 2: TutorTrac

Software especializado en gestión de centros de tutoría universitarios.

**Entidades/relaciones que maneja (a nivel conceptual):**
- Estudiante, Tutor, Materia, Sesión, Nota de sesión, Centro/Departamento.
- Registra visitas y notas por sesión, asociadas al estudiante y al tutor.

**Lo que aporta como referencia de modelado:** relación materia-tutor-sesión y registro histórico de notas por sesión, base para el historial de tutoría del contexto propuesto.
**Lo que no cubre:** no modela cupo máximo para sesiones grupales como concepto central, ni requisitos formales (nota mínima, aval) para que un estudiante se convierta en tutor.

### Comparación general (nivel de datos)

| Entidad/Relación necesaria | Starfish | TutorTrac |
|---|---|---|
| Rol dual Estudiante-Tutor | No | No |
| Departamento como entidad organizadora de tutores/materias/aulas | Parcial | Parcial |
| Modalidad como atributo de la sesión (presencial/virtual) | Sí | Limitado |
| Tabla puente Estudiante-Sesión para sesiones grupales con cupo | No | No |
| Requisitos/validación para habilitar tutor | No | No |
| Historial de sesiones y evaluación del tutor | Sí | Sí |

**Conclusión de la exploración:** ninguna de las herramientas revisadas modela de forma nativa el rol dual estudiante/tutor, los requisitos de habilitación como tutor, ni el cupo máximo en sesiones grupales. Esto respalda el diseño de un modelo de datos propio que incluya: especialización de Tutor a partir de Estudiante, entidad Departamento como eje organizador, atributo de modalidad condicionando la relación con Aula o Recurso virtual, y una tabla puente Inscripción para sesiones grupales.
