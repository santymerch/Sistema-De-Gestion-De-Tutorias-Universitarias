# Sistema de Gestión de Tutorías Universitarias - GESTORES DE BASES DE DATOS - BUC372605B

## Integrantes:

- Carlos Santiago Merchan Centeno 
- Steven Roldan Aldana
# Actividad de Exploración — Sistema de Gestión de Tutorías entre Pares, por Departamento, en Modalidad Híbrida

## Diagrama de entidad relacion

![image alt]()

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

Plataforma de éxito estudiantil que gestiona asesoría académica y agendamiento de tutorías, incluyendo tutorías entre pares.

**Entidades/relaciones que maneja (a nivel conceptual):**
- Estudiante, Asesor/Tutor (incluye tutor par), Cita, Modalidad de cita.
- Para agendar, el estudiante debe tener una conexión previa con el proveedor (tutor/asesor) dentro de su "red de éxito", y el proveedor debe tener bloques de disponibilidad individual o grupal abiertos <cite index="2-1">para poder agendar, un estudiante debe tener una conexión con el proveedor en Starfish, y el proveedor debe tener bloques de citas abiertos o disponibilidad de sesión grupal que incluya estudiantes con ese tipo de conexión</cite>.
- Contempla explícitamente el agendamiento de sesiones de **tutoría entre pares**, tanto individuales como grupales <cite index="3-1">permite programar una cita con un tutor par, de forma individual o grupal</cite>.

**Lo que aporta como referencia de modelado:** confirma que la disponibilidad debe modelarse como bloques abiertos por proveedor (tutor), y que la sesión grupal es un tipo de disponibilidad distinto a la individual — coincide con el atributo "cupo máximo" propuesto en el modelo.
**Lo que no cubre:** no expone públicamente cómo modela los requisitos para que un estudiante se habilite como tutor par, ni la gestión independiente por departamento.

### Herramienta 2: TutorTrac (Redrock Software)

Software especializado en gestión de centros de tutoría universitarios (también conocido como TracCloud).

**Entidades/relaciones que maneja (a nivel conceptual):**
- Estudiante, Tutor, Materia/Curso, Ubicación, Cita, Nota de sesión.
- Permite <cite index="18-1">que los estudiantes reserven sesiones según la disponibilidad del tutor, la materia, el curso, la ubicación y la franja horaria</cite>, y registra el contacto con estudiantes tanto en <cite index="16-1">ubicación física como en entorno en línea</cite>.
- Se integra con los sistemas académicos institucionales y <cite index="15-1">permite ver las clases programadas en un calendario y recuperar información de los estudiantes desde el portal institucional</cite>.

**Lo que aporta como referencia de modelado:** valida la relación Materia–Tutor–Ubicación–Sesión, y el registro histórico de notas por sesión como base del historial de tutoría del contexto propuesto.
**Lo que no cubre:** no está diseñado para tutoría *entre pares* con requisitos formales de habilitación (nota mínima, aval), sino para tutores vinculados administrativamente al centro; el cupo máximo en sesiones grupales no es un concepto central de su modelo público.

### Comparación general (nivel de datos)

| Entidad/Relación necesaria | Starfish | TutorTrac |
|---|---|---|
| Rol dual Estudiante-Tutor (tutoría entre pares) | Sí (tutor par) | No documentado |
| Departamento como entidad organizadora de tutores/materias/aulas | Parcial | Parcial |
| Modalidad como atributo de la sesión (presencial/virtual) | Sí | Parcial (ubicación física u online) |
| Bloques de disponibilidad individual vs. grupal por tutor | Sí | Parcial |
| Tabla puente Estudiante-Sesión para sesiones grupales con cupo | Implícito | No documentado |
| Requisitos/validación para habilitar tutor | No documentado | No documentado |
| Historial de sesiones y notas | Sí | Sí |

**Conclusión de la exploración:** Starfish confirma el concepto de tutoría entre pares con sesiones individuales/grupales, y TutorTrac confirma la relación materia–tutor–ubicación–sesión con historial. Sin embargo, ninguna documenta públicamente los requisitos formales para habilitar a un estudiante como tutor ni una gestión explícita por departamento con reglas propias. Esto respalda el diseño de un modelo de datos propio que incluya: especialización de Tutor a partir de Estudiante, entidad Departamento como eje organizador, atributo de modalidad condicionando la relación con Aula o Recurso virtual, y una tabla puente Inscripción para sesiones grupales.

## Fuentes

1. Starfish (EAB) — Página oficial de la solución. https://eab.com/solutions/starfish/
2. FAQs | Starfish Info — Penn State University. https://sites.psu.edu/starfishinfo/resources/faq/
3. Starfish for Student Success | Students — Columbia Business School. https://students.business.columbia.edu/office-of-student-affairs/academic-advising-and-student-success/starfish-student-success
4. TutorTrac Review: Features, Scheduling, Reporting, and Student Success Alternatives. https://www.aisaspa.com/tutortrac-review-features-scheduling-reporting-and-student-success-alternatives/
5. TutorTrac — Smyte (ficha de producto). https://smyte.com/software/tutortrac/
6. TutorTrac 2026 Pricing, Features, Reviews & Alternatives — GetApp. https://www.getapp.com/education-childcare-software/a/tutortrac/
7. TutorTrac Login — Redrock Software Corporation (sitio oficial del producto). https://tracdev.go-redrock.com/
