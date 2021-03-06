# ComproPago Python SDK

## Descripción

El paquete de `ComproPago Python SDK` le permite interactuar con el API de ComproPago en su aplicación.
También cuenta con los métodos necesarios para facilitar el desarrollo por medio de los servicios
más utilizados (SDK).

Con ComproPago puede recibir pagos en 7Eleven, Extra y más tiendas en todo México.

[Registrarse en ComproPago](https://compropago.com)

## Índice de Contenidos

- [Ayuda y soporte de ComproPago](#ayuda-y-soporte-de-compropago)
- [Requerimientos](#requerimientos)
- [Instalación ComproPago SDK](#instalación-compropago-sdk)
- [Documentación](#documentación)
- [Guía básica de uso](#guía-básica-de-uso)
- [Guía de versiones](#guía-de-versiones)


## Ayuda y Soporte de ComproPago

- [Centro de ayuda y soporte](https://compropago.com/ayuda-y-soporte)
- [Solicitar integración](https://compropago.com/integracion)
- [Guía para comenzar a utilizar ComproPago](https://compropago.com/ayuda-y-soporte/como-comenzar-a-usar-compropago)
- [Información de contacto](https://compropago.com/contacto)

## Requerimientos

* Python 3
* requests 2.9.1


## Instalación ComproPago Python SDK

### Instalacion por Pypi

Puede descargar facilmente el sdk de compropago con el manejador de paquetes de Python **Pypi** con el siguiente comando

```bash
pip3 install compropago
```

### Instalación por GitHub

Puedes descargar alguna de las versiones que hemos publicado:

- [Consultar Versiones Publicadas en GitHub](https://github.com/compropago/sdk-python/releases)

O si lo deseas puedes obtener el repositorio

```bash
#repositorio en su estado actual (*puede no ser versión estable*)
git clone https://github.com/compropago/sdk-python.git
```

## Documentación

**[API de ComproPago](https://compropago.com/documentacion/api)**

ComproPago te ofrece un API tipo REST para integrar pagos en efectivo en tu comercio electrónico o tus aplicaciones.

**[General](https://compropago.com/documentacion)**

Información de Horarios y Comisiones, como Transferir tu dinero y la Seguridad que proporciona ComproPAgo


**[Herramientas](https://compropago.com/documentacion/boton-pago)**

* Botón de pago
* Modo de pruebas/activo
* WebHooks
* Librerías y Plugins
* Shopify

## Guía básica de Uso

Se debe tener una cuenta activa de ComproPago.

[Registrarse en ComproPago](https://compropago.com)

### General

Para poder hacer uso de la librería es necesario incluir la librería principales de la libreria

```python
from compropago.client import Client
from compropago.factory.factory import Factory
```

### Configuración del Cliente

Para hacer uso de la libreria y llamados al API es necesario que primero configures tus Llaves de conexión y crees
un instancia de Client.
*Tus llaves las encontraras en su Panel de ComproPago en el menú Configuración.*

[Consulta Aquí tus Llaves](https://compropago.com/panel/configuracion)

```python
# @param string publickey     Llave publica correspondiente al modo de la tienda
# @param string privatekey    Llave privada correspondiente al modo de la tienda
# @param bool   live          Modo de la tienda (false = Test | true = Live)

client = Client(
    'pk_test_5989d8209974e2d62',  # publickey
    'sk_test_6ff4e982253c44c42',  # privatekey
    False                         # live
)
```

### Uso Básico de la libreria

#### Llamados al los servicios por SDK

Para poder hacer uso de los servicios de ComproPago, solo debes llamar a los métodos contenidos en la propiedad **api**
de la variable **client** como se muestra a continuación.


#### Métodos base del SDK

##### Crear una nueva orden de Pago


```python
"""
# @param [string|number] order_id    Identificador interno de la orden
# @param [string] order_name         Nombre de la orden, productos o servicio
# @param [float]  order_price        Monto de la orden
# @param [string] customer_name      Nombre del cliente
# @param [string] customer_email     Email del cliente
# @param [string] payment_type       (default = SEVEN_ELEVEN) Tienda en la cual se realizara el deposito (internal_name de objetos Provider)
# @param [string] currency           (default = MXN) Divisa de la moneda para elcobro (USD, EUR, GBP, MXN)
# @param [string] expiration_time    (default = nil) Fecha en formato Epoch para marcar la expiración de la orden
"""
order_info = {
    'order_id': 123,
    'order_name': 'M4 unit python',
    'order_price': 123.45,
    'customer_name': 'Eduardo Aguilar',
    'customer_email': 'eduardo.aguilar@compropago.com',
    'payment_type': 'SEVEN_ELEVEN',
    'currency': 'MXN',
    'expiration_time': '1484786210'
}

order = Factory.get_instance_of('PlaceOrderInfo', order_info)


"""
# Llamada al método 'place_order' del API para generar la órden
# @param [PlaceOrderInfo] order
# @return [NewOrderInfo]
"""
new_order = client.api.place_order(order)
```

###### Prototipo del método place_order()

```python
# @param [PlaceOrderInfo] info
# @return [NewOrderInfo]
def place_order(self, info)
```

##### Verificar el Estatus de una orden

Para verificar el estatus de una órden generada es necesario llamar al método **verify_order** que provee el atributo **api**
del objeto **Client** y el cual regresa una instancia **CpOrderInfo**. Este método recibe como parámetro el ID generado 
por ComproPago para cada orden. Tambien puede obtener este ID desde un objeto **NewOrderInfo** accediendo al
atributo **id**.

```python

# Guardar el ID de la orden
order_id = "ch_xxxx_xxx_xxx_xxxx"

# U obtenerlo de un objetdo NewOrderInfo
order_id = new_order.id


# Se manda llamar al metodo del API para recuperar la informacion de la orden
info = client.api.verify_order(order_id)
```

###### Prototipo del método verify_order()

```python
# @param [String] id
# @return [CpOrderInfo]
def verify_order(self, id)
```


##### Obtener el listado de las tiendas donde se puede realizar el Pago

Para obtener el listado de Proveedores disponibles para realizar el pago de las ordenes es necesario consultar el método
**list_providers** que se encuentra alojado en el atributo **api** del objeto **Client** y el cual regresa una instancia
de tipo **list** la cual contendrá objetos de tipo **Provider**

```python
providers = client.api.list_providers()
```

###### Prototipo del metodo get_providers()

```python
# @param [Float]  limit
# @param [string] currency
# @return [list]
def list_providers(self, limit = 0, currency = 'MXN')
```

##### Envío de instrucciones SMS

Para realizar el el envío de las instrucciones de compra vía SMS es necesario llamar al método **send_sms_instructions** 
que se encuentra alojado en el atributo **api** del objeto **Client** y el cual regresa una instancia de tipo **SmsInfo**

```python
# Número al cual se enviaran las instrucciones
phone_number = "55xxxxxxxx";

# Id de la órden de compra de cual se enviaran las instrucciones
order_id = "ch_xxxxx-xxxxx-xxxxx-xxxxx";

# Llamada al método del API para envío de las instrucciones
smsinfo = client.api.send_sms_instructions(phone_number , order_id)
```

###### Prototipo del método send_sms_instructions()

```python
# @param [String] number
# @param [String] id
# @return [SmsInfo]
def send_sms_instructions(self, number, id)
```

#### Webhooks

Los webhooks son de suma importancia para el proceso las órdenes de ComproPago, ya que ellos se encargaran de recibir 
las notificaciones del cambio en los estatus de las órdenes de compra generadas, tambien deberán contener parte de la 
lógica de aprobación en su tienda en línea. El proceso es el siguiente:

1. Cuando una órden cambia su estatus, nuestra plataforma le notificará a cada una de las rutas registradas, dicho 
cambio con la información de la orden modificada en formato JSON.
2. Deberá recuperar este JSON en una cadena de texto para posteriormente convertirla a un objeto de tipo **CpOrderInfo**
haciendo uso de la clase Factory que proporciona el SDK de la siguiente forma:

```python
info = Factory.get_instance_of('CpOrderInfo', cadena_obtenida)
```

3. Generar la lógica de aprobación correspondiente al estatus de la órden.

##### Crear un nuevo Webhook

Para crear un nuevo Webhook en la cuenta, se debe de llamar al método **create_webhook** que se encuentra alojado en el 
atributo **api** del objeto **Client** y el cual regresa una instancia de tipo **Webhook**

```python
# Se pasa como paramtro la URL al webhook
webhook = client.api.create_webhook('http://sitio.com/webhook')
```

###### Prototipo del método create_webhook()

```python
# @param [String] url
# @return [Webhook]
def create_webhook(self, url)
```

##### Actualizar un Webhook

Para actualizar la url de un webhook, se debe de llamar al método **update_webhook** que se encuentra alojado en el 
atributo **api** del objeto **Client** y el cual regresa una instancia de tipo **Webhook**

```python
updated_webhook = client.api.update_webhook(webhook.id, 'http://sitio.com/nuevo_webhook', 'secondary')
```

###### Prototipo del método update_webhook()

```python
# @param [String] url
# @param [String] webhook_id
# @param [string] type (secondary | primary)
# @return [Webhook]
def update_webhook(self, webhook_id, url, type)
```

##### Eliminar un Webhook

Para eliminar un webhook, se debe de llamar al método **delete_webhook** que se encuentra alojado en el atributo **api**
del objeto **Client** y el cual regresa una instancia de tipo **Webhook**

```python
deleted_webhook = client.api.delete_webhook(webhook.id)
```

###### Prototipo del método delete_webhook()

```python
# @param [String] webhook_id
# @return [Webhook]
def delete_webhook(self, webhook_id)
```

##### Desactivar un webhook 

Para desactivar un webhook y evitar que reciba notificaciones sin eliminarlo debe de ocupar la función **deactive_webhook**
que se encuentra alojado en el atributo **api** del objeto **Client** y el cual regresa una instancia de tipo **Webhook**

```python
updated_webhook = client.api.deactive_webhook(webhook.id)
```

###### Prototipo del método deactive_webhook()

```python
# @param [string] webhook_id
# @return [Webhook]
def deactive_webhook(self, webhook_id)
```

##### Obtener listado de Webhooks registrados

Para obtener la lista de webhooks registrados en una cuenta, se debe de llamar al método **list_webhook** que se 
encuentra alojado en el atributo **api** del objeto **Client** y el cual regresa una instancia de tipo **list** la cual
contiene objetos de tipo **Webhook**

```python
webhooks = client.api.list_webhooks()
```

###### Prototipo del método list_webhook()

```python
# @return [list]
def list_webhooks(self)
```


