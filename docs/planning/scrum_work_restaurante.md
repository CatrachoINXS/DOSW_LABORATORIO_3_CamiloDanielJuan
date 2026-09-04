# Desglose del Trabajo del Restaurante - Sakura Sushi

* **Sistema:** Plataforma Web de Gestión Operativa para Restaurante Japonés
* **Integrantes:** Juan Manuel Garzón, Cristian Camilo Ortiz, Daniel Camilo Mosquera
* **Curso:** DESARROLLO Y OPERACIONES DE SOFTWARE (DOSW)

---

## ÉPICA

### **EPIC-01: Gestión Operativa Integral de Pedidos y Cocina para Sakura Sushi**
* **Descripción:** Plataforma web unificada que digitaliza el ciclo completo de atención del restaurante japonés.
Permite la consulta de la carta en tiempo real, la personalización de rolls por ingredientes, la confirmación de pedidos
desde el salón, la gestión de comandas en la barra de sushi agrupadas en tandas de máximo 6 unidades y el control
secuencial de estados de preparación.

---

## FEATURES

### **FEATURE-01: Personalización y Armado de Pedidos en Salón**
* **Épica Padre:** EPIC-01
* **Descripción:** Permite a clientes y meseros armar órdenes, consultar la disponibilidad de ingredientes en tiempo real
y personalizar rolls seleccionando ingredientes base y adicionales antes de enviar la comanda a la cocina.

### **FEATURE-02: Gestión de Cocina por Tandas y Flujo de Preparación**
* **Épica Padre:** EPIC-01
* **Descripción:** Administra el tablero de cocina de la barra de sushi, estructurando la cola de trabajo en tandas de
producción de máximo 6 rolls y gestionando el cambio de estados de cada pedido respetando el flujo operativo.

---

## HISTORIAS DE USUARIO Y SUBTAREAS

### **HISTORIA DE USUARIO 1 (HU-01)**
* **Código:** STORY-01 (Asociada a Requerimiento SS-09)
* **Feature Padre:** FEATURE-01
* **Título:** Personalización de Roll de Sushi por Ingredientes
* **Prioridad:** **Alta**
    * *Justificación:* El armado de rolls a pedido es una regla de negocio central del concepto japonés y un factor clave
  de diferenciación en la experiencia del cliente.
* **Formato:** Como cliente, quiero seleccionar los ingredientes de mi roll para personalizar mi pedido según mis
preferencias e intolerancias alimentarias.

#### **Criterios de Aceptación:**
1. **Dado** que el cliente está en la pantalla de armado de roll, **cuando** selecciona ingredientes disponibles, 
**entonces** el sistema calcula y actualiza el precio final sumando la base y los adicionales seleccionados.
2. **Dado** que un ingrediente se encuentra marcado como "agotado" en inventario, **cuando** el cliente visualiza las 
opciones, **entonces** el sistema deshabilita la selección de dicho ingrediente y muestra una alerta.

#### **Subtareas:**
* **SUB-01:** Diseñar e implementar el componente de interfaz gráfica para la selección de ingredientes con estado visual
(disponible/agotado) y contador de precio en tiempo real.
* **SUB-02:** Crear el endpoint de la API `POST /api/v1/rolls/custom` para recibir los ingredientes elegidos, validar el
precio total y estructurar el objeto del roll personalizado.
* **SUB-03:** Implementar la lógica de validación en backend que verifique la disponibilidad de stock en base de datos
antes de permitir agregar el roll al carrito.

---

### **HISTORIA DE USUARIO 2 (HU-02)**
* **Código:** STORY-02 (Asociada a Requerimiento SS-05)
* **Feature Padre:** FEATURE-01
* **Título:** Confirmación y Envío del Pedido a Cocina
* **Prioridad:** **Alta**
    * *Justificación:* Es el disparador fundamental del proceso operativo; sin confirmar la comanda, la cocina no recibe
  órdenes para iniciar la producción.
* **Formato:** Como mesero, quiero confirmar el pedido de una mesa para enviarlo automáticamente al tablero de la barra 
de sushi sin necesidad de comandas físicas en papel.

#### **Criterios de Aceptación:**
1. **Dado** que una mesa tiene una cuenta abierta y un pedido con al menos un ítem, **cuando** el mesero presiona 
"Confirmar Pedido", **entonces** el estado del pedido pasa a `RECIBIDO` y se despliega en el tablero de cocina en menos 
de 2 segundos.
2. **Dado** que el pedido está vacío o la cuenta de la mesa se encuentra cerrada, **cuando** el mesero intenta confirmar, 
**entonces** el sistema bloquea el envío y muestra un mensaje de error.

#### **Subtareas:**
* **SUB-04:** Construir la pantalla de resumen de la orden con el desglose de ítems, totales y botón de confirmación en
la app del mesero.
* **SUB-05:** Desarrollar el endpoint `PUT /api/v1/pedidos/{id}/confirmar` que valide las precondiciones 
(pedido no vacío y cuenta abierta) y actualice el estado a `RECIBIDO`.
* **SUB-06:** Configurar la transmisión de eventos en tiempo real (WebSockets/Server-Sent Events) para actualizar el 
tablero de la cocina instantáneamente.

---

### **HISTORIA DE USUARIO 3 (HU-03)**
* **Código:** STORY-03 (Asociada a Requerimientos SS-10 y Regla de Negocio Propia)
* **Feature Padre:** FEATURE-02
* **Título:** Agrupación Automática de Rolls en Tandas de Cocina
* **Prioridad:** **Media**
    * *Justificación:* Garantiza el cumplimiento de la regla operativa de la barra de sushi de no saturar la preparación
  con más de 6 unidades por tanda.
* **Formato:** Como personal de cocina, quiero visualizar los rolls entrantes organizados en tandas de máximo 6 unidades
para mantener un flujo de preparación ordenado en la barra.

#### **Criterios de Aceptación:**
1. **Dado** que entran pedidos confirmados al tablero de cocina, **cuando** el sistema procesa los rolls solicitados, 
**entonces** los agrupa en bloques o tandas de máximo 6 unidades respetando el orden de llegada (FIFO).
2. **Dado** que una tanda actual alcanza el cupo límite de 6 rolls, **cuando** ingresa un nuevo roll, **entonces** el 
sistema asigna automáticamente dicho roll a una nueva tanda de preparación.

#### **Subtareas:**
* **SUB-07:** Diseñar el maquetado del tablero Kanban de cocina con columnas organizadas por tandas de producción.
* **SUB-08:** Desarrollar el algoritmo de backend para empaquetar y dividir las listas de rolls en grupos de máximo 6 
unidades manteniendo la precedencia del pedido.
* **SUB-09:** Elaborar pruebas unitarias para el algoritmo de agrupación de tandas garantizando que nunca se excedan los
6 rolls por bloque.

---

### **HISTORIA DE USUARIO 4 (HU-04)**
* **Código:** STORY-04 (Asociada a Requerimiento SS-06)
* **Feature Padre:** FEATURE-02
* **Título:** Control Secuencial del Estado de Preparación
* **Prioridad:** **Alta**
    * *Justificación:* Otorga visibilidad completa del estado del plato tanto al personal de cocina como a los meseros
  en el salón.
* **Formato:** Como personal de cocina, quiero actualizar el estado de cada pedido a medida que avanza su preparación
para reflejar el progreso en tiempo real.

#### **Criterios de Aceptación:**
1. **Dado** que un pedido está en el tablero de cocina, **cuando** el cocinero interactúa con él, **entonces** el 
sistema le permite avanzar el estado únicamente en el orden estricto: `RECIBIDO` → `EN PREPARACIÓN` → `LISTO` → `ENTREGADO`.
2. **Dado** que un pedido ha alcanzado el estado `ENTREGADO`, **cuando** cualquier usuario intenta modificar su estado
nuevamente, **entonces** el sistema rechaza la acción.

#### **Subtareas:**
* **SUB-10:** Crear la interfaz interactiva con botones contextuales de cambio de estado en la comanda virtual del tablero
de cocina.
* **SUB-11:** Desarrollar el endpoint `PATCH /api/v1/pedidos/{id}/estado` con validación rígida para evitar saltos 
ilícitos entre estados.
* **SUB-12:** Implementar la persistencia de auditoría para registrar la fecha, hora exacta y el id del usuario que 
realiza cada cambio de estado.

---
### **ESTIMACIÓN POR PLANNING POKER**

#### **Estimación (puntos) a cada historia de usuario:** 

- **HU-01:** 5 puntos.
- **HU-02:** 5 puntos.
- **HU-03:** 3 puntos.
- **HU-04:** 4 puntos.

#### **Enlace del video de Planning Poker a la historia grabada:** https://drive.google.com/file/d/1RbC_6bFqwq5cWOjUiNY7acVhzZzxP2bb/view?usp=sharing