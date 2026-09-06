# Rozo-post1-U2
Post-contenido — Exportación de reportes académicos con patrones creacionales justificados

# Post-contenido — Unidad 2: Patrones Creacionales

## Descripción ## 
Repositorio del post-contenido de la Unidad 2 de Patrones de Diseño de Software — Sexto Semestre. Un único proyecto Maven (`exportador-reportes/`) que resuelve la exportación de reportes académicos en múltiples formatos (Parte 1) y se extiende con configuración compleja y evaluación de Singleton (Parte 2).

## Cómo ejecutar
```bash
cd exportador-reportes
mvn compile
mvn exec:java '-Dexec.mainClass=com.patrones.u2.Main' 
``` 

## Decisiones de diseño 
Decisión 1 — Factory Method vs. Abstract Factory (Parte 1)
Patrón elegido: Abstract Factory.

Justificación: El dominio exige garantizar la consistencia entre los componentes de un documento (el cuerpo ReportBody y el encabezado/pie ReportHeaderFooter). Abstract Factory permite agrupar la creación de esta familia de productos para que una exportación en un formato concreto (PDF, Excel o HTML) mantenga coherencia visual y estructural sin mezclar implementaciones entre formatos. Se descartó Factory Method porque tratar el documento como un único producto rígido o usar métodos de fábrica independientes no asegura de manera declarativa que el cuerpo y el encabezado/pie pertenezcan estrictamente a la misma familia de formato.

Decisión 2 — Mecanismo de extensibilidad de formatos (Parte 1)
Opción elegida: Registro central dinámico (ReportFactoryRegistry) basado en Map<String, Supplier<ReportFormatFactory>>.

Justificación: Cumple con el principio Open/Closed (OCP) al permitir registrar nuevos formatos (como el formato CSV planificado) mediante el método register() en tiempo de ejecución, sin modificar la lógica interna de la clase. Se descartaron las estructuras condicionales switch o if-else encadenadas porque obligarían a modificar la clase del registro cada vez que se agregue un nuevo formato de reporte, violando directamente OCP.

Decisión 3 — Builder vs. constructor telescópico vs. setters (Parte 2)
Patrón elegido: Builder.

Justificación: Para un objeto con 8 parámetros opcionales y 1 obligatorio, los constructores telescópicos o con 9 parámetros dificultan la legibilidad, fuerzan a recordar el orden exacto de argumentos del mismo tipo (String, boolean) y provocan explosión de combinaciones. Por otro lado, la mutabilidad con setters sueltos permite dejar el objeto en estados parciales o inconsistentes sin un punto centralizado de validación. El patrón Builder permite construir el objeto ExportConfig de forma fluida, inmutable y centraliza la validación de consistencia en el método build() (por ejemplo, validar que compress=true exija un outputPath).

Decisión 4 — ¿ReportFactoryRegistry necesita ser Singleton? (Parte 2)
Conclusión: NO necesita ser Singleton.

Justificación: 

-Identidad de objeto: El registro no se pasa como parámetro, no implementa interfaces, no se inyecta por constructor ni requiere ser reemplazado por un mock en pruebas unitarias; basta con invocar sus miembros estáticos directamente.

-Fuente única de verdad e inicialización: El atributo Map estático ya garantiza una única fuente de verdad a nivel de JVM desde la carga de la clase (classloading) de forma liviana, sin requerir trabajo costoso de E/S ni la ceremonia de un getInstance(), constructor privado con guardas ni sincronización.

-Escenarios futuros: Convertirlo en Singleton impediría escenarios futuros realistas como plataformas multi-tenant donde cada institución necesite su propio registro independiente.

## Herramientas utilizadas
Java 17, Apache Maven, VS Code, Git, GitHub

## Conclusiones
La implementación de este proyecto permitió comprender que los patrones creacionales no deben aplicarse por costumbre o preferencia sintáctica, sino mediante una evaluación objetiva del problema de diseño. Abstract Factory demostró ser ideal para proteger la coherencia de familias de productos, mientras que el uso de un registro dinámico con Map y Supplier garantizó el cumplimiento del principio OCP sin caer en condicionales rígidos. Asimismo, la incorporación de Builder facilitó la creación de objetos de configuración complejos de forma inmutable y validada, y la evaluación de Singleton reconfirmó que las clases utilitarias estáticas suelen ser suficientes cuando no se requiere identidad de objeto ni polimorfismo.