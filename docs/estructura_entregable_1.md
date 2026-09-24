# Diseño y Estado del Arte: Consulta privada de inteligencia de amenazas mediante Oblivious Transfer

**Nota para el equipo:** Este documento debe tener una extensión total de entre 6 y 8 páginas. El objetivo de este entregable es planificar, justificar y diseñar el sistema antes de escribir el código definitivo.

---

## 1. Escenario y Contexto
*En esta sección deben describir el problema que están resolviendo.*
*   **Descripción del caso de uso:** Explicar la necesidad de un analista de consultar indicadores de amenazas en el catálogo de un proveedor sin revelar qué indicador específico está investigando.
*   **La solución propuesta a alto nivel:** Describir brevemente cómo se usará el catálogo cifrado pre-descargado y cómo el Oblivious Transfer (OT) entra en juego para entregar solo la clave de descifrado requerida.

## 2. Modelo de Adversario
*Aquí definen contra quién se están defendiendo y qué capacidades tiene, respetando las exclusiones del enunciado ("Fuera de alcance").*
*   **El Servidor (Proveedor):** ¿Qué intenta hacer el servidor? (Ej. es "honesto pero curioso"; sigue el protocolo pero intenta deducir qué registro pidió el cliente analizando los mensajes matemáticos).
*   **El Cliente (Analista):** ¿Qué intenta hacer un cliente malicioso? (Ej. intentar engañar al protocolo para obtener más de una clave de descifrado por ejecución).
*   **El Intermediario (Red):** Un atacante que observa o modifica el tráfico de red.
*   **Exclusiones explícitas:** Aclarar que *no* se protegerá contra servidores completamente maliciosos, ni se ocultará la IP, ni la frecuencia de consultas (como indica el enunciado).

## 3. Propiedades de Seguridad
*Basado en el modelo anterior, ¿qué garantías criptográficas debe cumplir el sistema?*
*   **Privacidad del receptor (Selección oculta):** Garantizar matemáticamente que el servidor aprende `0` información sobre el índice (del 1 al N) elegido por el cliente.
*   **Privacidad del emisor (Transferencia única):** Garantizar que el cliente aprende exactamente la clave solicitada y `0` información sobre las otras N-1 claves.
*   **Integridad y Autenticidad:** Garantizar que si el catálogo cifrado o las claves transferidas son modificadas por un tercero (o por errores), el cliente lo detecte (uso de AEAD).

## 4. Estado del Arte y Protocolo Derivado
*Esta es la sección matemática/criptográfica central.*
*   **Marco Teórico:** Breve resumen conceptual del OT 1-out-of-N basado en Diffie-Hellman, citando la referencia obligatoria de *Boneh y Shoup ($11.6)* y *Naor y Pinkas*.
*   **Especificación del Protocolo:** 
    *   Diagrama de secuencia o lista de pasos detallando exactamente qué envía el cliente (Petición) y qué responde el servidor (Respuesta).
    *   Detallar las fórmulas matemáticas que usarán para este intercambio (sin copiar y pegar; adaptado a su nomenclatura).
*   **Justificación de Seguridad:** Explicar con palabras/matemáticas simples por qué se cumplen las propiedades de seguridad de la Sección 3 con este protocolo.

## 5. Arquitectura del Sistema
*Diseño de los componentes de software (separación de responsabilidades).*
*   **Componentes principales:** Describir el *Importador*, el *Servidor* y el *Cliente* (CLI/Web).
*   **Flujo general de datos:** Cómo fluye la información desde la creación del catálogo sintético, la descarga por parte del cliente, hasta la consulta OT.
*   **Línea base (Consulta Convencional):** Breve diseño de cómo será la consulta HTTP normal que se usará para comparar el rendimiento.

## 6. Formatos y Serialización
*¿Cómo viajan los datos y cómo se guardan?*
*   **Estructura del Catálogo:** Diseño del archivo/base de datos que contiene los registros cifrados, IDs, y versión.
*   **Formato de Mensajes:** Definir la estructura exacta (ej. JSON, Protobuf, binario) de los mensajes de petición y respuesta del OT.
*   **Identificación y Sesiones:** Cómo se identificarán unívocamente las sesiones frescas, la versión del catálogo, el índice y los algoritmos para evitar ataques de repetición o confusión de dominio.

## 7. Selección de Bibliotecas y Primitivas Criptográficas
*Justificación técnica de las herramientas a usar.*
*   **Grupo Matemático:** Elección del grupo de orden primo (ej. Ristretto255 basado en RFC 9496 u otro) y la biblioteca criptográfica que lo implementa.
*   **Primitivas Simétricas:** Especificar el algoritmo AEAD (ej. AES-GCM, ChaCha20-Poly1305) para cifrar los registros y encapsular las claves.
*   **Derivación de Claves (KDF):** Qué función usarán para derivar claves simétricas a partir de los elementos del grupo DH (ej. HKDF) e incluir la "separación de dominios".

## 8. Plan de Pruebas y Experimentación
*Cómo van a demostrar que el software funciona y es seguro.*
*   **Pruebas Funcionales (Positivas):** Solicitar el primer índice, el último, uno al azar; repetir la misma consulta y verificar que los mensajes de red cambien pero el registro final sea el mismo.
*   **Pruebas de Seguridad (Negativas/Manipulación):** 
    *   Plan para intentar abrir claves no solicitadas (esperando rechazo).
    *   Plan para inyectar elementos de grupo mal formados o índices inválidos.
    *   Plan para corromper el catálogo y verificar que el cliente lance error.
*   **Plan de Experimentación (Rendimiento):** Metodología para medir latencia, carga de red y operaciones matemáticas para catálogos de 500, 1000, 2500 y 5000 registros, y cómo se compararán con la consulta HTTP ordinaria.