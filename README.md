# Rozo-post1-U2
Post-contenido — Exportación de reportes académicos con patrones creacionales justificados

# Sistema de Exportación de Reportes Académicos

## Decisiones de Diseño

### Decisión 1: Patrón Creacional Seleccionado
* **Patrón Elegido:** Abstract Factory.
* **Justificación:** El dominio exige garantizar la consistencia entre los componentes de un documento (el cuerpo `ReportBody` y el encabezado/pie `ReportHeaderFooter`). Abstract Factory permite agrupar la creación de esta familia de productos para que una exportación en un formato concreto (PDF, Excel o HTML) mantenga coherencia visual y estructural sin mezclar implementaciones entre formatos.
* **Alternativa Descartada:** Factory Method o un único creador monolítico. Se descartó debido a que tratar el documento como un único producto rígido o usar métodos de fábrica independientes no asegura de manera declarativa que el cuerpo y el encabezado/pie pertenezcan estrictamente a la misma familia de formato.

### Decisión 2: Mecanismo de Extensibilidad sin Violación de OCP
* **Mecanismo Elegido:** Registro central dinámico (`ReportFactoryRegistry`) basado en `Map<String, Supplier<ReportFormatFactory>>`.
* **Justificación:** Cumple con el principio Open/Closed (OCP) al permitir registrar nuevos formatos (como el formato CSV planificado) mediante el método `register()` en tiempo de ejecución, sin modificar la lógica interna ni el código existente del registro.
* **Alternativa Descartada:** Estructuras condicionales `switch` o `if-else` encadenadas. Se descartó porque obligaría a modificar la clase del registro cada vez que se agregue un nuevo formato de reporte, violando directamente el principio OCP.
