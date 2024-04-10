<h1 align="center"><a href="https://netvux.com/">Facturación Electrónica en linea República Dominicana / NETVUX </a> </h1>

## Descripción de Ambientes
Un ambiente es un contexto o entorno creado con la finalidad de definir el curso y
funcionamiento de determinados servicios en cada uno de ellos, en NETVUX se estarán utilizando dos (2) ambientes:

- Pre-certificación: publica los servicios a efectos de que los contribuyentes
puedan practicar y realizar pruebas de adecuación e integración de sus sistemas, almacenando los envíos por un periodo de 60 días. No tiene validez fiscal.
- Producción: ambiente productivo donde todo envío y operación tiene validez fiscal.

## Recepción del ECF
Comprende la recepción, validación, asignación del consecutivo y la incorporación de la firma electrónica.
La verificación consiste en aplicar las reglas y validaciones vigentes por la DGII.

Si el resultado de la verificación es satisfactorio, se procede a enviar a la DGII.
Todo ECF aceptado es remitido al emisor inmediatamente.


## Endpoints:

**Autenticación**: Esta ruta recibe un nombre de usuario y un token que identifican al usuario en NETVUX.
Si la información de usuario es correcta retorna un token de acceso asociado a una fecha de emisión y una fecha de expiración(5 minutos).
Este token es necesario enviarlo en lo adelante para realizar cualquier comunicacion con NETVUX.

<h4>Example</h4>

```python
import requests
url_val_semilla = "https://sandbox.netvux.com/TesteCF/api/Autenticacion"

headers_semilla = {
    'Authorization': 'Bearer {}'.format("0f9a2d4d7ff04516d66c4aa7e1d438173b4fea15"),
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
sesión válida y un xml simplificado con la estructura de la DGII.
Si el documento es aceptado retorna un XML de la siguente forma.

<h4>Example</h4>

```python
import requests

headers_semilla = {
    "accept": 'application/json',
    'Authorization': 'Bearer 03a4a99c01db7a494e12fe4ba7f9af2995a63feco1737035156',
}

api_url = 'https://sandbox.netvux.com/TesteCF/api/ecf'
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
    <trackId>872537f2-14b4-4e19-9998-e5c0c45e70ae</trackId>
</MensajeEnviado>
```

**Consulta XML**: Esta ruta recibe un token de acceso asociado a una
sesión válida y una trackId. Retorna un xml firmado
y con la estructura completa de la DGII.

<h4>Example</h4>

```python
import requests

headers_semilla = {
    "accept": 'application/json',
    'Authorization': 'Bearer 03a4a99c01db7a494e12fe4ba7f9af2995a63feco1737035156',
}

api_url = 'https://sandbox.netvux.com/TesteCF/api/TrackId?TrackId=872537f2-14b4-4e19-9998-e5c0c45e70ae'
response = requests.post(api_url, headers=headers_semilla)

```

**Consulta Estado ECF**: Esta ruta es responsable de retornar el estado de procesamiento o validez del e‐CF
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

api_url = 'https://sandbox.netvux.com/TesteCF/api/consultaestado'
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

## Responses
200: Exito en la creacion de un ecf.

400: Respuesta de error por validaciones.

404: Respuesta de error debido a q no se he encontrado el recurso al que se intenta acceder.

505: Respuesta de error debido a un error interno en el sistema.

### Reference

- [Documentación sobre e-CF
](https://dgii.gov.do/cicloContribuyente/facturacion/comprobantesFiscalesElectronicosE-CF/Paginas/documentacionSobreE-CF.aspx)
