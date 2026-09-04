| **SakuraSushi**<br><sub>Desarrollo y Operaciones de Software</sub> | <h3 align="center">  ANÁLISIS DE REQUERIMIENTOS  </h3> | **Fecha:**  | 27/08/2026 |
| :----------------------------------------------------------------- | :----------------------------------------------------: | :---------- | :--------: |
|                                                                    |                                                        | **Página:** |   1 de 1   |

---

## Requerimientos funcionales

| Codigo | Descripcion                                                                                 | Actor principal    |
| ------ | ------------------------------------------------------------------------------------------- | ------------------ |
| SS-01  | Consultar la carta con el estado de disponibilidad de cada plato                            | Cliente            |
| SS-02  | Agregar ítems de un pedido con la cuenta abierta                                            | Mesero             |
| SS-03  | Modificar ítems de un pedido con la cuenta abierta                                          | Mesero             |
| SS-04  | Quitar ítems de un pedido con la cuenta abierta                                             | Mesero             |
| SS-05  | Confirmar un pedido y enviarlo automáticamente al tablero de cocina                         | Mesero             |
| SS-06  | Cambiar el estado de un pedido desde cocina (RECIBIDO → EN PREPARACIÓN → LISTO → ENTREGADO) | Personal de cocina |
| SS-07  | Cerrar la cuenta de una mesa y registrar el pago                                            | Mesero             |
| SS-08  | Generar el reporte de ventas del día por plato y por mesero                                 | Administrador      |
| SS-09  | El cliente puede escoger los ingredientes de un roll                                        | Cliente            |
| SS-10  | Organizar los rolls de la barra en tandas de máximo 6 unidades                              | Personal de cocina |

## Requerimientos no funcionales

| Codigo    | Categoria      | Descripcion                                                                                        |
| --------- | -------------- | -------------------------------------------------------------------------------------------------- |
| SS-RNF-01 | Seguridad      | Controlar el acceso según el rol: cliente, mesero, cocina y administrador.                         |
| SS-RNF-02 | Rendimiento    | El tablero de cocina debe mostrar un pedido nuevo en menos de 2 segundos                           |
| SS-RNF-03 | Disponibilidad | El sistema opera durante todo el servicio sin reinicios (12 horas continuas)                       |
| SS-RNF-04 | Usabilidad     | Un cliente nuevo completa su primer pedido en máximo 4 pantallas                                   |
| SS-RNF-05 | Auditabilidad  | Todo cambio de estado de un pedido queda registrado con usuario y hora                             |
| SS-RNF-06 | Consistencia   | Mantener actualizada la disponibilidad de los platos cuando un ingrediente se marque como agotado. |

---

### **FUNCIONALIDAD**
### Requerimiento 1
| Código:                   | SS-05                                                                                                                                                                                |
| :------------------------ | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Nombre:**               | Confirmar un pedido y enviarlo automaticamente al tablero de cocina                                                                                                                  |
| **Descripción:**          | El mesero puede confirmar un pedido cuando esté completo. Al confirmarlo, el sistema lo envía automáticamente al tablero de cocina para que el personal pueda comenzar a prepararlo. |
| **Actores involucrados:** | Mesero, Personal de cocina                                                                                                                                                           |
| **Precondiciones:**       | El mesero debe haber iniciado sesion, debe existir un pedido con al menos 1 item y la cuenta de la mesa debe estar abierta.                                                          |

#### **DATOS DE ENTRADA**

| Nombre         | Descripción                                | Tipo de campo | Reglas / Aplicación                            | Obligatorio |
| :------------- | :----------------------------------------- | :------------ | :--------------------------------------------- | :---------: |
| id del pedido  | Identificador del pedido a confirmar       | Numero        | El id debe corresponder a un pedido que exista |     Si      |
| items          | rolls que hacen parte del pedido           | List          | debe contener por lo menos un item             |     Si      |
| numero de mesa | Número de la mesa asociada al pedido       | Numero        | La mesa debe tener una cuenta abierta          |     Si      |
| confirmacion   | Accion del mesero para confirmar el pedido | boolean       | verdadera para confirmar                       |     Si      |

#### DATOS DE SALIDA

| Nombre                  | Descripción                                                  | Tipo de campo | Reglas / Aplicación                        | Obligatorio |
| :---------------------- | :----------------------------------------------------------- | :------------ | :----------------------------------------- | :---------: |
| estado pedido           | Estado en el que se encuentra el pedido despues de confirmar | String        | debe quedar como RECIBIDO                  |     si      |
| pedido enviado a cocina | Indica si el pedido fue enviado al tablero                   | boolean       | cuando el pedido se confirma correctamente |     si      |

#### FLUJO BÁSICO

| Paso | Actor  | Descripción                                   | Excepciones                                               |
| ---- | ------ | --------------------------------------------- | --------------------------------------------------------- |
| 1    | Mesero | Selecciona el pedido que desea confirmar.     | -                                                         |
| 2    | Mesero | Revisa los ítems y la información del pedido. | Si el pedido esta vacio. Flujo alterno 1                  |
| 3    | Mesero | Confirma el pedido.                           | Si la cuenta está cerrada el pedido no puede confirmarse. |

#### FLUJO ALTERNO

| Paso | Actor  | Descripción                                     | Excepciones |
| ---- | ------ | ----------------------------------------------- | ----------- |
| 1    | Mesero | Excepcion de que el pedido no puede estar vacio | —           |

---
### Requerimiento 2
| Código:                   | SS-06                                                                                                                                  |
| :------------------------ | :------------------------------------------------------------------------------------------------------------------------------------- |
| **Nombre:**               | Cambiar el estado de un pedido desde cocina                                                                                            |
| **Descripción:**          | El personal de la cocina puede actualizar el estado de cada pedido siguiendo el orden RECIBIDO -> EN PREPARACION -> LISTO -> ENTREGADO |
| **Actores involucrados:** | Mesero, Personal de cocina                                                                                                             |
| **Precondiciones:**       | El personal de cocina debe haber iniciado sesion, debe existir un pedido enviado a la cocina.                                          |

#### DATOS DE ENTRADA

| Nombre            | Descripción                                         | Tipo de campo | Reglas / Aplicación                                  | Obligatorio |
| :---------------- | :-------------------------------------------------- | :------------ | :--------------------------------------------------- | :---------: |
| id del pedido     | Identificador del pedido a cambiar de estado        | Numero        | El id debe corresponder a un pedido que exista       |     Si      |
| estado actual     | estado en el que se encuentra actualmente el pedido | String        | Debe ser RECIBIDO, EN PREPARACIÓN, LISTO o ENTREGADO |     Si      |
| usuario de cocina | Persona que hace el cambio                          | Objeto        | -                                                    |     Si      |

#### DATOS DE SALIDA

| Nombre       | Descripción                         | Tipo de campo | Reglas / Aplicación                    | Obligatorio |
| :----------- | :---------------------------------- | :------------ | :------------------------------------- | :---------: |
| nuevo estado | Estado actualizado del pedido       | String        | debe ser el siguiente estado permitido |     si      |
| fecha y hora | Momento en el que se hizo el cambio | fecha y hora  | -                                      |     si      |


#### FLUJO BASICO

| Paso | Actor              | Descripción                                         | Excepciones                            |
| ---- | ------------------ | --------------------------------------------------- | -------------------------------------- |
| 1    | Personal de cocina | Selecciona un pedido del tablero                    | —                                      |
| 2    | Personal de cocina | Revisa el estado actual                             | Si ya está entregado, Flujo alterno 1  |
| 3    | Personal de cocina | Selecciona el siguiente estado permitido            | Si intenta saltar un estado se rechaza |
| 4    | SakuraSushi        | Actualiza el estado y registra fecha/hora y usuario | -                                      |

#### FLUJO ALTERNO

| Paso | Actor              | Descripción                                      | Excepciones                          |
| ---- | ------------------ | ------------------------------------------------ | ------------------------------------ |
| 1    | Personal de cocina | Intenta cambiar el estado de un pedido entregado | Se bloquea el cambio y muestra aviso |

#### REGLAS DE NEGOCIO

| No. | Descripcion                                                                                                 |
| --- | ----------------------------------------------------------------------------------------------------------- |
| 1   | El estado de los pedidos solo avanza en este orden: recibido, en preparacion, listo y entregado, sin saltos |
| 2   | Todo cambio queda registrado con usuario y hora                                                             |
| 3   | Un pedido entregado no puede volver a cambiar de estado                                                     |
---
### Requerimiento 3
| Código:                   | SS-09                                                                                                                                                                                                                         |
| :------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Nombre:**               | Escoger los ingredientes de un roll                                                                                                                                                                                           |
| **Descripción:**          | El cliente puede personalizar un roll escogiendo los ingredientes que quiere en su pedido. El sistema debe mostrar los ingredientes que puede seleccionar y permitirle al cliente armar su roll antes de agregarlo al pedido. |
| **Actores involucrados:** | Cliente, Mesero, Personal de cocina                                                                                                                                                                                           |
| **Precondiciones:**       | Deben existir ingredientes disponibles, la cuenta de la mesa debe estar abierta                                                                                                                                               |

#### DATOS DE ENTRADA

| Nombre       | Descripción                                         | Tipo de campo | Reglas / Aplicación                             | Obligatorio |
| :----------- | :-------------------------------------------------- | :------------ | :---------------------------------------------- | :---------: |
| id cliente   | Identificador del cliente que arma el roll          | Numero        | -                                               |     Si      |
| ingredientes | ingredientes que el cliente quiere incluir          | String        | solo se pueden escoger ingredientes disponibles |     Si      |
| id pedido    | identificador del pedido al que se agregara el roll | numero        | el pedido debe estar abierto                    |     Si      |

#### DATOS DE SALIDA

| Nombre             | Descripción                                    | Tipo de campo | Reglas / Aplicación                                                | Obligatorio |
| :----------------- | :--------------------------------------------- | :------------ | :----------------------------------------------------------------- | :---------: |
| roll personalizado | roll creado con los ingredientes seleccionados | Objeto        | debe contener lo escogido por el cliente                           |     si      |
| precio             | precio del roll personalizado                  | decimal       | se calcula segun los ingredientes que el cliente haya seleccionado |     si      |
| estado             | estado de preparacion del roll                 | string        | —                                                                  |     si      |

#### FLUJO BASICO

| Paso | Actor       | Descripción                             | Excepciones                                    |
| :--- | :---------- | :-------------------------------------- | :--------------------------------------------- |
| 1    | Cliente     | Selecciona opcion de armar roll         | —                                              |
| 2    | Cliente     | Selecciona los ingredientes que desea   | Si un ingrediente esta agotado Flujo alterno 1 |
| 3    | SakuraSushi | Calcula el precio según lo seleccionado | —                                              |
| 4    | Cliente     | Agrega el roll personalizado al pedido  | —                                              |

#### FLUJO ALTERNO

| Paso | Actor   | Descripción                                | Excepciones                                          |
| :--- | :------ | :----------------------------------------- | :--------------------------------------------------- |
| 1    | Cliente | Intenta seleccionar un ingrediente agotado | El sistema lo bloquea y muestra solo los disponibles |

#### REGLAS DE NEGOCIO

| No. | Descripcion                                                                                                 |
| --- | ----------------------------------------------------------------------------------------------------------- |
| 1   | Solo se pueden elegir ingredientes disponibles |
| 2   | El precio se calcula sumando el valor base con el valor de los ingredientes adicionales                                                          |
| 3   | El roll entra a la cola de preparación respetando el limite de tandas de 6 unidades maximo.                                                   |

---

## Preguntas de análisis crítico

- **a)** Analizando bien la tabla de los requerimientos funcionales creemos que deberiamos detallar un poco mas los requerimientos SS-02, SS-03, SS-04 que hablan sobre agregar, modificar y eliminar items del pedido. Hay que aclarar que eso se puede hacer mientras el pedido este en estado RECIBIDO.
  
- **b)** No vimos requerimientos que se contradigan entre si como tal.
- **c)** SS-01 y SS-02 consideramos son los mas importantes, porque son la base para poder crear los pedidos, sin pedidos no tenemos que confirmar ni que cambiar de estado.
- **d)** El SS-08 de generar reporte de ventas. Consideramos que no es tan relevante para la operacion diaria porque no interviene directamente en el ciclo de los pedidos. 

## Vínculo de Mockups a Requerimientos Funcionales

| Código del Requerimiento | Nombre del Requerimiento | Pantalla(s) Asociada(s) | Ruta del Mockup |
|---------------------------|--------------------------|-------------------------|-----------------|
| SS-01 | Consultar carta con disponibilidad | Pantalla 2 - Carta Digital | ![alt text](/DOSW_LABORATORIO_3_CamiloDanielJuan/docs/images/screen-2.png) |
| SS-09 | Escoger ingredientes de un roll | Pantalla 3 - Personalizar Roll | ![alt text](/DOSW_LABORATORIO_3_CamiloDanielJuan/docs/images/screen-3.png) |
| SS-02 / SS-03 / SS-04 | Agregar/Modificar/Eliminar ítems | Pantalla 4 - Carrito | ![alt text](/DOSW_LABORATORIO_3_CamiloDanielJuan/docs/images/screen-4.png) |
| SS-05 | Confirmar y enviar a cocina | Pantalla 5 - Confirmación | ![alt text](/DOSW_LABORATORIO_3_CamiloDanielJuan/docs/images/screen-5.png) |
| SS-06 / SS-10 | Cambiar estado / Tandas de 6 | Pantalla 6 - Tablero Cocina | ![alt text](/DOSW_LABORATORIO_3_CamiloDanielJuan/docs/images/screen-6.png) |
| SS-RNF-06 | Control de inventario | Pantalla 7 - Gestión Inventario | ![alt text](/DOSW_LABORATORIO_3_CamiloDanielJuan/docs/images/screen-7.png) |
| SS-08 | Reporte de ventas | Pantalla 8 - Reportes | ![alt text](/DOSW_LABORATORIO_3_CamiloDanielJuan/docs/images/screen-8.png) |
| RNF-01 | Inicio de sesión por roles | Pantalla 1 - Login | ![alt text](/DOSW_LABORATORIO_3_CamiloDanielJuan/docs/images/screen-1.png) |