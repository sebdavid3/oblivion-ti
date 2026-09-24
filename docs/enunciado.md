Banco de Proyectos I+D en Criptografía Aplicada 

# **Proyecto 8. Consulta privada de inteligencia de amenazas mediante Oblivious Transfer** 

## **Enunciado** 

Construir una plataforma en la que un proveedor publique un catálogo de registros de inteligencia de amenazas y un analista pueda recuperar uno de ellos sin revelar al servidor cuál seleccionó. A la vez, una consulta deberá permitir al cliente abrir solamente el registro elegido. Para que la aplicación sea manejable, los registros estarán cifrados de antemano con claves independientes y el protocolo de Oblivious Transfer se utilizará para entregar únicamente la clave correspondiente. El cliente descargará el catálogo cifrado completo para que una solicitud posterior del archivo no revele indirectamente su elección. 

## **Producto de software esperado** 

- Importador que convierta una colección sintética o pública de indicadores en un catálogo cifrado y versionado. 

- Servidor que desempeñe el rol de emisor en un protocolo OT 1-out-of-N basado en Diffie-Hellman. 

- Cliente CLI o web que seleccione un record_id, ejecute el protocolo y descifre localmente el registro obtenido. 

- Modo de consulta convencional que sirva como línea base de funcionalidad, latencia y ancho de banda. 

- Reporte que muestre el costo de ocultar la selección a medida que aumenta el catálogo. 

## **Requisitos técnicos mínimos** 

- Un servidor y un cliente por ejecución; entre 500 y 5.000 registros de longitud fija entre 128 y 512 bytes. 

- OT 1-out-of-N basado en Diffie-Hellman, siguiendo la construcción pedagógica de Boneh y Shoup. 

- Grupo de orden primo y operaciones implementadas por una biblioteca criptográfica reconocida. 

- Una clave simétrica independiente por registro y AEAD para proteger tanto registros como claves transferidas. 

- Sesiones frescas, separación de dominios y vinculación del catálogo, la versión y el índice en las derivaciones y datos asociados. 

- El cliente debe descargar el catálogo cifrado completo; consultar un objeto individual al servidor anularía la privacidad de selección. 

## **Decisiones de diseño que debe resolver el equipo** 

- Estudiar la construcción de referencia y derivar una especificación interoperable de mensajes sin copiar una receta del enunciado. 

- Justificar por qué el servidor no aprende el índice y por qué el cliente solo puede obtener una clave por ejecución bajo el modelo adoptado. 

- Seleccionar el grupo y definir validación, serialización y rechazo de elementos inválidos. 

- Diseñar el catálogo, el mapa público record_id-índice, el versionado y el manejo de sesiones repetidas o interrumpidas. 

- Explicar el costo lineal del protocolo y separar la privacidad criptográfica de la información revelada por IP, tiempo y frecuencia. 

## **Demostración, pruebas y evaluación experimental** 

- Recuperar correctamente el primer registro, el último y varios índices seleccionados al azar. 

- Repetir una consulta al mismo índice con sesiones frescas y obtener transcripciones diferentes y 

Página 19 de 25 

Banco de Proyectos I+D en Criptografía Aplicada 

el mismo registro. 

- Intentar abrir claves de índices no seleccionados y comprobar que el cifrado autenticado las rechaza. 

- Rechazar elementos de grupo mal formados, índices inválidos, sesiones duplicadas y versiones incompatibles. 

- Detectar modificaciones del catálogo, de las claves encapsuladas y de los registros cifrados. 

- Medir latencia, operaciones de grupo, petición, respuesta y descarga inicial para 500, 1.000, 2.500 y 5.000 registros, comparando con una consulta HTTP ordinaria. 

## **Fuera de alcance** 

- PIR, OT extension, adaptive OT basado en OPRF o bases con millones de registros. 

- Clientes o servidores completamente maliciosos y pruebas de seguridad componible. 

- Ocultamiento de IP, frecuencia, momento o volumen de consultas; pagos o licenciamiento real. 

## **Referencias clave** 

- Boneh, D. y Shoup, V. A Graduate Course in Applied Cryptography, §11.6: Oblivious transfer based on Diffie-Hellman. Enlace. 

- Naor, M. y Pinkas, B. (2001). Efficient Oblivious Transfer Protocols. SODA, 448-457. Enlace. 

- de Valence, H. et al. (2023). The ristretto255 and decaf448 Groups. RFC 9496. Enlace. 

Página 20 de 25 

