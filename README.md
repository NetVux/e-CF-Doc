<h1 align="center"><a href="https://NETWUX/">Facturación Electrónica República Dominicana  / NETVUX </a> </h1>

## Descripción de Ambientes: 
Un ambiente es un contexto o entorno creado con la finalidad de definir el curso y
funcionamiento de determinados servicios en cada uno de ellos, en NETVUX se estarán utilizando dos (2) ambientes:

- Pre-certificación:
- Producción:

## Endpoints:

**ValidarSemilla**: Esta ruta recibe un nombre de usuario y una clave (encriptado en HS256) que identifican al usuario en NETVUX.
Si la información de usuario es correcta retorna un token de acceso asociado a una fecha de emisión y una fecha de expiración(5 minutos).
Este token es necesario enviarlo en lo adelante para realizar cualquier comunicacion con NETVUX.

<h4>Example</h4>

```python
import requests
url_val_semilla = "https://server_demo/TesteCF/api/ValidarSemilla"

headers_semilla = {
    'Authorization': 'Bearer {}'.format("eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e30.njLWdDtB-uEBaz5iMqTYtGJ5iBfvkWOKKrEERAr1nwY"),
}

data = {
    'user': 'demo',
}

response = requests.post(url_val_semilla, headers=headers_semilla, data=data)
```

Retorna un objeto que contiene un string de autenticación (token) asociado a una fecha de emisión y una fecha de expiración:

```json
{"token": "03a4a99c01db7a494e12fe4ba7f9af2995a63feco1737035156", "issued": "2024-01-17 13:45:56", "expired": "24-50-17 13:50:56"}
```

**Recepcion ECF**: Esta ruta recibe un token de acceso asociado a una
sesión válida y un xml con la estructura de la DGII.
Si el token es valido retorna un XML de la siguente forma.

<h4>Example</h4>

```python
import requests

headers_semilla = {
    "accept": 'application/json',
    'Authorization': 'Bearer 03a4a99c01db7a494e12fe4ba7f9af2995a63feco1737035156',
}

api_url = 'server_demo/TesteCF/api/ecf'
files = {
    'xml': (xmlname, xml, 'text/xml')
}
response = requests.post(api_url, headers=headers_semilla, files=files)

```

Retorna el estado del documento, consecutivo, mensaje de la DGII, Codigo de seguridad y la fecha de firma:

```xml
<MensajeEnviado>
    <encf>E310000000084</encf>
    <message_detail_dgii>TrackId: 064b3a97-be47-455c-96ad-82c412362fa8
Codigo: 1
Estado: Aceptado
Mensajes: {"Mensaje": {"Valor": null, "Codigo": "0"}}</message_detail_dgii>
    <estado>delivered_accepted</estado>
    <security_code>aHRGQk</security_code>
    <sign_date>17-01-2024 15:09:37</sign_date>    
</MensajeEnviado>
```
**Consulta Estado ECF**: Esta ruta recibe responsable de retornar el estado de procesamiento o validez del e‐CF
tentativo enviado exclusivamente mediante el servicio web de recepción de e‐CF.

<h4>Example</h4>

```python
import requests

headers_semilla = {
    "accept": 'application/json',
    'Authorization': 'Bearer 03a4a99c01db7a494e12fe4ba7f9af2995a63feco1737035156',
}

data = {
    'rncemisor': '1234',
    'ncfelectronico': 'E310000000084',
}

api_url = 'server_demo/TesteCF/api/consultaestado'
response = requests.post(api_url, headers=headers_semilla)

```


```json
{
"trackId": "string",
"codigo": 0,
"estado": "string",
"rnc": "string",
"eNCF": "string",
"secuenciaUtilizada": true,
"fechaRecepcion": "string",
"mensajes": [
{
"valor": "string",
"codigo": 0
}
]
}
```
