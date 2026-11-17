# Laboratorio: Arquitectura Serverless y Aislamiento de Eventos (EventBridge & Step Functions)

**Objetivo:** Configurar una arquitectura orientada a eventos utilizando Amazon API Gateway, AWS Lambda, Amazon EventBridge y AWS Step Functions, resolviendo la colisión de eventos en un entorno de cuenta compartida mediante la personalización del patrón de enrutamiento.

---

## 1. Actualización de la Función Lambda (Aislamiento de Eventos)
En esta captura se evidencia la modificación del código fuente de la función Lambda inicial (`receive-order-tinavarela`). Para resolver el problema de colisión de eventos en el entorno compartido de AWS, se personalizó el atributo `Source` dentro del método `put_events`. Al asignar el valor unívoco `"demo.pedidos.tinavarela"`, se garantiza que el evento publicado en el bus posea una firma de origen exclusiva, estableciendo el primer paso para una arquitectura completamente aislada.

![Actualización de Lambda](./imagen2.lab.jpg)

---

## 2. Configuración del Patrón de Enrutamiento en Amazon EventBridge
La imagen detalla la actualización de la regla de enrutamiento dentro de Amazon EventBridge. El patrón de eventos (*Event pattern*) fue ajustado estrictamente para filtrar y capturar únicamente los eventos cuyo origen coincida con `"demo.pedidos.tinavarela"`. Esta configuración crea un canal de comunicación directo y exclusivo entre la Lambda inicial y la máquina de estados, evitando que la ejecución se vea alterada por peticiones generadas por otros usuarios en la misma cuenta de AWS.

![Patrón de Eventos](./imagen1.lab.jpg)

---

## 3. Invocación del API Gateway y Validación del Cliente (Postman)
Se presenta la prueba de integración final (End-to-End) desde el cliente HTTP Postman. Se inyectó un *payload* de prueba en formato JSON simulando los datos de un cliente mediante una petición POST dirigida al Amazon API Gateway. La recepción de un código de estado HTTP `200 OK`, acompañado de un mensaje de confirmación y un `orderId` generado, certifica que la petición fue procesada correctamente por el API y delegada con éxito a la función Lambda para su transmisión al bus de eventos.

![Prueba en Postman](./imagen4.lab.jpg)

---

## 4. Trazabilidad y Culminación Exitosa en AWS Step Functions
Esta evidencia final ilustra el diagrama de flujo de la máquina de estados (`step-tinavarela`) correspondiente a la ejecución del pedido aislado. La iluminación en verde de todos los nodos del flujo (tales como *ValidateOrder*, *CheckInventory* y *ProcessPayment*) hasta llegar al *SuccessState*, demuestra que el sistema procesó la lógica de negocio sin interrupciones ni desvíos hacia los estados de fallo. Esto comprueba el éxito absoluto de la implementación de la arquitectura *serverless*, logrando un flujo de eventos asíncrono, robusto y correctamente segmentado.

![Ejecución Exitosa Step Functions](./imagen3.lab.jpg)
