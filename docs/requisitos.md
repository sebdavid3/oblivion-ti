### Entregables

* Entregable 1 - Diseño y estado del arte (40 %): documento de 6-8 páginas con escenario, modelo de adversario, propiedades, protocolo derivado de las referencias, formatos, arquitectura, selección de bibliotecas y plan de pruebas.
* Entregable 2 - Producto e informe final (60 %): repositorio ejecutable, informe de 8-12 páginas, pruebas automatizadas, scripts y datos de experimentación, y demostración reproducible.
* El informe final puede corregir el diseño inicial, pero debe explicar las desviaciones entre la propuesta y la implementación.

### Requisitos comunes del producto

* README con instalación desde cero, dependencias fijadas, comandos de ejecución y guion de demostración.
* Separación comprensible entre interfaz, lógica del protocolo, primitivas criptográficas, persistencia y pruebas.
* Formatos versionados, serialización inequívoca e identificación explícita de algoritmos y parámetros.
* Pruebas positivas, negativas y de manipulación; una única demostración exitosa no reemplaza las pruebas automatizadas.
* Uso de datos sintéticos o autorizados y prohibición de registrar secretos, contraseñas, claves o plaintexts sensibles.
* Uso preferente de bibliotecas mantenidas; solo se implementarán operaciones internas cuando el proyecto lo exija con fines pedagógicos.
* Ejecución local o mediante contenedores reproducibles; no se requiere despliegue público.

### Criterios comunes de evaluación

| Componente | Peso | Evidencia esperada |
| :--- | :--- | :--- |
| Producto funcional | 30% | Cumplimiento del producto mínimo, integración, manejo de errores y experiencia de uso. |
| Diseño y precisión técnica | 25% | Protocolo, supuestos, formatos, modelo de adversario y decisiones justificadas desde las referencias. |
| Criptografía aplicada | 20% | Uso correcto de primitivas, claves, nonces, dominios, validaciones y límites de seguridad. |
| Pruebas y experimentos | 15% | Casos positivos y negativos, métricas reproducibles y análisis de resultados. |
| Informe y reproducibilidad | 10% | Claridad, referencias, README, scripts y calidad del repositorio. |

### Uso responsable

Los proyectos de criptoanálisis se ejecutarán exclusivamente sobre servicios locales, claves y conjuntos generados para el curso. No se autoriza escaneo, prueba o explotación de sistemas externos. Los equipos deberán presentar los resultados como evidencia dentro de un modelo definido, no como certificaciones de seguridad para sistemas reales.