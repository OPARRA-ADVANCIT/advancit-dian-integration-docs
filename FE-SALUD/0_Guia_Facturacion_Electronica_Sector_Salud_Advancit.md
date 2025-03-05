# Manual de Desarrollo para Consumo de Webservice Sector Salud

## 1. **Introducción**
Este documento técnico describe cómo los desarrolladores deben consumir el Web Service de facturación electrónica del Sector Salud, 
el cual permite enviar documentos a la DIAN para su validación previa, mediante el método `send_document`. 
La comunicación con el servicio se realiza mediante una petición SOAP que incluye información en formato JSON encriptado en base64.

### **Requisitos previos**
- **URL del WSDL**: 
  - **Pruebas**: Permite realizar pruebas infinitas sin repercusión. URL: `https://mff.advancit.co/facturaelectronicav2/ws_electronic_invoice_salud_v2.wsdl`
  - **Producción**: URL para operaciones reales: `por definir`
- Implementar la seguridad requerida, como certificados digitales si aplica.
- Uso de la librería SOAP en PHP (u otro lenguaje que soporte SOAP).

## 2. **Especificaciones del Webservice**

### **Métodos disponibles**
- **send_document**: Este método envía a la DIAN la información de facturas electrónicas y otros documentos.

### **Parámetros de entrada**
Los datos que se deben enviar en la petición SOAP incluyen las siguientes secciones codificadas en base64:

- **CABDOC**: Información de la cabecera del documento.
- **CLIDOC**: Información del cliente.
- **DETDOC**: Detalle del documento.
- **DETALLEPRO**: Detalle de productos.
- **DETIMP**: Detalle de impuestos.
- **ADIDOC**: Información adicional del documento.
- **DESCDOC**: Detalle de descuentos.
- **DATSAL**: Datos de salud.
- **PREPAI**: Información sobre el pago previo, que incluye el monto abonado o anticipado por el paciente o aseguradora.
- **PURSAL**: Datos relacionados con archivos.
- **PUESAL**: Datos relacionados con archivos adicionales.


Cada una de estas secciones se incluye como un elemento `item` dentro del cuerpo SOAP.

## 3. **Proceso de Integración**

### **Construcción de la Petición SOAP**
La petición al servicio web debe seguir la estructura proporcionada en el WSDL, utilizando formato XML con la cabecera y el cuerpo SOAP. El cuerpo debe contener los datos JSON codificados en base64:

##### **Ejemplo de Estructura SOAP**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope 
    SOAP-ENV:encodingStyle="http://schemas.xmlsoap.org/soap/encoding/" 
    xmlns:SOAP-ENC="http://schemas.xmlsoap.org/soap/encoding/" 
    xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" 
    xmlns:ns1="urn:documents" 
    xmlns:ns2="http://xml.apache.org/xml-soap" 
    xmlns:xsd="http://www.w3.org/2001/XMLSchema" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <SOAP-ENV:Body>
        <ns1:send_document>
            <sendoc xsi:type="ns2:Map">
                <item>
                    <key xsi:type="xsd:string">sedaws</key>
                    <value xsi:type="xsd:string">{JSON codificado}</value>
                </item>
                <item>
                    <key xsi:type="xsd:string">secado</key>
                    <value xsi:type="xsd:string">{JSON codificado}</value>
                </item>
                <!-- Otros items - campos como sededo, sedepr, sedeim -->
            </sendoc>
        </ns1:send_document>
    </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

### **Formato de los Datos JSON**

Los datos dentro del XML están en formato JSON, que debe estar correctamente estructurado. Cada sección representa un conjunto de datos que debe ser enviado.

## Campos de la Cabecera del Documento (DATOEMP)
| Descripción                        | Nombre   | Ejemplo                       | Especificación  | Nota                                                    | Oblig. |
|------------------------------------|----------|-------------------------------|-----------------|---------------------------------------------------------|--------|
| Nit de la empresa                  | NITEMP   | 123456789                     | CHAR(9)          |                                                        | SI     |
| Dígito de verificación             | DIGVER   | 8                             | CHAR(1)          |                                                        | SI     |
| Nombre de la empresa               | NOMEMP   | ABC+SOLUTIONS+SAS              | CHAR(255)        |                                                        | SI     |
| Razón social                       | RASOCI   | ABC+SOLUTIONS+SAS              | CHAR(255)        |                                                        | SI     |
| Correo electrónico                 | COREMP   | info@abcsolutions.com          | CHAR(200)        |                                                        | SI     |
| Fecha de registro                  | FECREG   | 2020-06-30                    | DATE             |                                                        | SI     |
| Nombre del software                | SOFTWA   | EASYBILLING                    | CHAR(200)        |                                                        | SI     |
| ID del software                    | IDESOF   | 11111111-2222-3333-4444-555555555555 | CHAR(36)         |                                                        | SI     |
| PIN del software                   | PINSOT   | 67890                         | CHAR(5)          |                                                        | NO     |
| URL de envío                       | URLENV   | https:\/\/dian-fake.com\/WcfDianCustomerServices.svc?wsdl | CHAR(255) |                                                        | SI     |
| Prefijo                            | PREFIJ   | FACT                          | CHAR(4)          |                                                        | SI     |
| Número de resolución               | NUMRES   | 9876543210                    | CHAR(30)         |                                                        | SI     |
| Fecha de la resolución             | FECRES   | 2021-01-01                    | DATE             |                                                        | SI     |
| Rango inicial de la resolución     | RESINI   | 100000000                     | CHAR(30)         |                                                        | SI     |
| Rango final de la resolución       | RESFIN   | 200000000                     | CHAR(30)         |                                                        | SI     |
| Fecha inicio de resolución         | FEREIN   | 2021-02-01                    | DATE             |                                                        | SI     |
| Fecha fin de resolución            | FEREFI   | 2031-02-01                    | DATE             |                                                        | SI     |
| Dirección de la empresa            | DIREMP   | CALLE 123 # 45-67 BOGOTA       | CHAR(255)        |                                                        | SI     |
| Teléfono de la empresa             | TELEMP   | 6019876543                    | CHAR(10)         |                                                        | SI     |
| Certificado                        | CERTIF   | CertificadoFicticio.p12        | CHAR(255)        |                                                        | NO     |
| Contraseña del certificado         | PASCER   | abc1234XYZ                    | CHAR(16)         |                                                        | NO     |
| Clave técnica                      | TECKEY   | a1b2c3d4e5f67890abcdef1234567890 | CHAR(40) |                                                      | NO     |
| Tesid                              | TESIDE   | aabbccdd-eeff-1122-3344-556677889900 | CHAR(36) |                                                    | NO     |
| Departamento Empresa               | DEPEMP   | 68 					| CHAR(36) | Ver tabla Anexo-Tecnico-Factura-Electronica-de-Venta-vr-1-9.pdf (13.2.9.2 Departamentos (ISO 3166-2:CO): cbc:CountrySubentity, cbc:CountrySubentityCode)   | NO     |
| Ciudad Empresa                     | CIUEMP   | 77068276				| CHAR(36) | Ver tabla Anexo-Tecnico-Factura-Electronica-de-Venta-vr-1-9.pdf (13.2.9.3 Municipios: cbc:CityName) | NO     |
| ID de Registro 		     | REGID   	|  					| CHAR(36) |                                                    | NO     |
| Registro Nombre                    | REGNAM   |  					| CHAR(36) |                                                    | NO     |

NOTA: Teniendo en cuenta que el proceso de facturacion electronica ha experimentado multiples modificaciones, se han mantenido algunos campos de versiones anteriores los cuales no se veran reflejados en el xml final de envio, pero teniendo en cuenta que no se han descartado del todo, se mantienen en la presente estructura debido a su posible puesta en marcha en un futuro.

##### **Ejemplo de DATOEMP**
```json

{
  "DATOEMP": [
    {
      "NITEMP": "123456789",
      "DIGVER": "8",
      "NOMEMP": "ABC+SOLUTIONS+SAS",
      "RASOCI": "ABC+SOLUTIONS+SAS",
      "COREMP": "info@abcsolutions.com",
      "FECREG": "2020-06-30",
      "SOFTWA": "EASYBILLING",
      "IDESOF": "11111111-2222-3333-4444-555555555555",
      "PINSOT": "67890",
      "URLENV": "https://dian-fake.com/WcfDianCustomerServices.svc?wsdl",
      "PREFIJ": "FACT",
      "NUMRES": "9876543210",
      "FECRES": "2021-01-01",
      "RESINI": "100000000",
      "RESFIN": "200000000",
      "FEREIN": "2021-02-01",
      "FEREFI": "2031-02-01",
      "DIREMP": "CALLE 123 # 45-67 BOGOTA",
      "TELEMP": "6019876543",
      "CERTIF": "CertificadoFicticio.p12",
      "PASCER": "abc1234XYZ",
      "TECKEY": "a1b2c3d4e5f67890abcdef1234567890",
      "TESIDE": "aabbccdd-eeff-1122-3344-556677889900",
      "DEPEMP": "68",
      "CIUEMP": "77068276",
      "REGID": "",
      "REGNAM": ""
    }
  ]
}
```
Este JSON debe codificarse en base64 antes de ser incluido en el valor correspondiente dentro del XML SOAP.

## Campos de la Cabecera del Documento (CABDOC)
| Descripción                        		| Nombre   | Ejemplo          | Especificación | Nota                                          | Oblig. |
|-----------------------------------------------|----------|------------------|----------------|-----------------------------------------------|--------|
| Nit de la empresa que factura 	     	| NITEMP   | 900225722        | CHAR(30)       | No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Numero de resolución autorizada  	  	| NUMRES   | 1234567891000AA  | CHAR(40)       | No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Tipo de documento a enviar         		| TDOCU    | 01               | CHAR(2)        | 01: Facturas, 02: Notas débito, 03: Notas crédito. | SI     |
| Numero de factura                  		| NUMERO   | 15111            | CHAR(20)       | No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Fecha de generación de factura     		| FECDOC   | 2019-05-31       | CHAR(40)       | No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Fecha de vencimiento de factura    		| FECVEN   | 2019-05-31       | CHAR(40)       | No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Nombre del vendedor                		| NOMVEN   | Inversiones Piedra Del Sol S. A. | CHAR(255) | No completar con nada ni a la izquierda ni a la derecha. | NO     |
| Código de moneda de la factura     		| MONEDA   | COP              | CHAR(3)        | Ver tabla Anexo-Tecnico-Factura-Electronica-de-Venta-vr-1-9.pdf (13.2.8.3 Moneda (ISO 4217): @currencyID)   | SI     |
| Tipo de envío para notas crédito   		| TIPCRU   |                  | CHAR(1)        | Aplica solo para notas crédito.                       | SI     |
| Código sucursal o tienda           		| CODSUC   |                  | CHAR(50)       | Código sucursal o tienda.                            | NO     |
| Número de factura referenciada     		| NUMREF   |                  | CHAR(40)       | Solo aplica para notas crédito de tipo F.               | SI     |
| Código de formato de diseño        		| FORIMP   |                  | CHAR(2)        | Código de formato de diseño para el PDF.          | NO     |
| Clasificación del documento        		| CLADET   |                  | CHAR(1)        | D: Detallado, U: Único.                           | NO     |
| Formas de Pago                     		| FORPAG   |                  | CHAR(2)        | Ver tabla Anexo-Tecnico-Factura-Electronica-de-Venta-vr-1-9.pdf (13.2.8.4.1 Formas de Pago: cbc:PaymentMeans/ID) | SI     |
| Orden de compra                    		| ORDENC   |                  | CHAR(40)       | Si requiere manejar número de orden de compra.    | NO     |
| Número de remisión                 		| NUREMI   |                  | CHAR(40)       | Si requiere manejar número de remisión.           | NO     |
| Nota de recepción                  		| NORECE   |                  | CHAR(40)       | Si requiere manejar nota de recepción.            | NO     |
| EAN tienda entrega                 		| EANTIE   |                  | CHAR(40)       | Código EAN de tienda de entrega.                   | NO     |
| Código moneda origen               		| COMODE   |                  | CHAR(3)        | Código moneda de origen.                           | SI si es factura de exportación |
| Código moneda destino              		| COMOHA   |                  | CHAR(3)        | Código moneda de destino.                          | SI si es factura de exportación |
| Factor cálculo moneda              		| FACCAL   |                  | CHAR(12)       | Valor tasa de cambio de moneda extranjera.         | SI si es factura de exportación |
| Fecha de tasa de cambio            		| FETACA   |                  | CHAR(20)       | Fecha en la que se tomó la tasa de cambio.         | SI si es factura de exportación |
| Fecha del documento referenciado   		| FECREF   |                  | CHAR(20)       | Fecha en la que se generó el documento referenciado. | SI para notas crédito |
| Observaciones documento referenciado 		| OBSREF |                  | CHAR(200)      | Observaciones del documento referenciado.         | SI para notas crédito |
| Observaciones generales            		| OBSERV   |                  | CHAR(200)      | Si requiere una observación en la factura.        | NO     |
| Texto de la factura                		| TEXDOC   |                  | CHAR(950)      | Texto adicional de la factura.                    | NO     |
| Motivo devolución DIAN             		| MODEDI   |                  | CHAR(1)        | Ver tabla Anexo-Tecnico-Factura-Electronica-de-Venta-vr-1-9.pdf (13.2.5.3 Documento CreditNote – Nota Crédito) | SI para notas crédito |
| Número de entrega                  		| NUMENT   |                  | CHAR(10)       | Si requiere manejar número de entrega.            | NO     |
| Número de factura DIAN             		| NDIAN    |                  | CHAR(18)       | Número de la factura para pruebas.                 | NO     |
| Organización de ventas             		| SOCIED   |                  | CHAR(40)       | Código de la organización de ventas.               | NO     |
| Código del vendedor               		| CODVEN   |                  | CHAR(40)       | Si requiere código de vendedor en la factura.      | NO     |
| Motivo de devolución               		| MOTDEV   |                  | CHAR(200)      | Motivo de devolución de la nota crédito.           | NO     |
| Subtotal del documento             		| SUBTO   | 46218.00         | DEC(15,2)      | Valor sin impuestos, descuentos y anticipos.     | SI     |
| Base para calcular impuestos       		| BASIMP   | 46218.00         | DEC(15,2)      | Valor sobre el cual se calculan los impuestos.    | SI     |
| Total a pagar                      		| TOTFAC   | 54999.42         | DEC(15,2)      | Resultado de SUBTOT + TOTIMP.                   | SI     |
| Total de impuestos                 		| TOTIMP   | 8781.42          | DEC(15,2)      | Multiplicación de SUBTOT por el porcentaje de impuestos. | SI     |
| Valor total de descuentos          		| TOTDES   | 0.00             | DEC(15,2)      |                                                 | NO     |
| Días para realizar el pago         		| DIPAPA   | 0                | INT(4)         | Si se otorgan días para el pago de la factura.    | NO     |
| Tipo de operación                  		| TIPOPE   |                  | CHAR(2)        | Ver tabla Anexo-Tecnico-Factura-Electronica-de-Venta-vr-1-9.pdf (13.2.5.1 Documento Invoice – Factura electrónica) o  Ver tabla LINEAMIENTOS PARA LA GENERACIÓN, VALIDACIÓN Y ENVÍO DEL REGISTRO INDIVIDUAL DE PRESTACION DE SERVICIOS DE SALUD – RIPS, COMO SOPORTE DE LA FACTURA ELECTRÓNICA DE VENTA – FEV EN SALUD (4.7.1. FACTURA ELECTRÓNICA DE VENTA)        | SI     |
| Medios de Pago		     		| MEDPAG   | 40		      | | Ver tabla Anexo-Tecnico-Factura-Electronica-de-Venta-vr-1-9.pdf (13.2.8.4.2 Medios de Pago: cbc:PaymentMeansCode)  |SI|
| Usuario                            		| USUAR    |                   | CHAR(30)       |                                                 | SI     |
| Clave						| CLAVE    |                   | CHAR(30)       |                                                 | SI     |
| Fecha de inicio del periodo de prestación de servicios	| INPESD   |                   | varchar(100)	|Fecha de inicio del periodo de prestación de servicios.                                             | Sí              | 
| Hora de inicio del periodo de prestación de servicios	| INPEST   |                   | varchar(100)	| Hora de inicio del periodo de prestación de servicios.                                       | Sí              |
| Fecha de finalización del periodo de prestación de servicios	    	| INPEND   |                   | varchar(100)	| Fecha de finalización del periodo de prestación de servicios.                      | Sí              | 
| Hora de finalización del periodo de prestación de servicios		| INPENT   |                   | varchar(100)	| Hora de finalización del periodo de prestación de servicios.                         | Sí              |
| Identificador del análisis del recurso	| ANDREID  |                   | Alfanumérico      |  Código único que identifica un análisis del recurso utilizado o entregado.                         |No              |
| Datos del análisis del recurso		| ANDREDA  |                   | Texto             |  Información específica relacionada con el análisis del recurso.                                     |No              |
| Fecha del análisis del recurso		| ANDREDT  |                   | Fecha (YYYY-MM-DD)| Fecha en la que se realizó el análisis del recurso.                                                 | No              |
| Cupo nominal asignado A			| CUNAMA   |                   | Numérico          | Cantidad límite asignada al recurso en el periodo, tipo A.                                          | No              |
| Valor del cupo nominal asignado A		| CUVALA   |                   | Numérico          | Valor monetario correspondiente al cupo asignado A.                                                 | No              |
| Cupo nominal asignado B			| CUNAMB   |                   | Numérico          | Cantidad límite asignada al recurso en el periodo, tipo B.                                          | No              |
| Valor del cupo nominal asignado B		| CUVALB   |                   | Numérico          | Valor monetario correspondiente al cupo asignado B.                                                 | No              | 
| Cantidad de cigarrillos sin nicotina		| CIGSHN   |                   | Numérico          | Número de cigarrillos sin nicotina entregados o facturados.                                         | No              |
| Cantidad de cigarrillos con nicotina		| CIGCSH   |                   | Numérico          | Número de cigarrillos con nicotina entregados o facturados.                                         | No              |
| Impuesto sobre turismos diarios		| IPTURD   |                   | Numérico          | Valor del impuesto sobre turismos aplicable por día, si corresponde.                                | No              |
| Identificador de preparación			| PREPID   |                   | Numérico          | Código único que identifica el proceso de preparación del documento o servicio.                     | NO              |
| Monto de preparación				| PREPAM   |                   | Numérico          | Valor monetario asociado al costo de preparación de los bienes o servicios.                        | No              |
| Descuento por preparación			| PREDRE   |                   | Numérico          | Valor del descuento aplicado al costo de preparación de los bienes o servicios.                    | No              |

NOTA: Teniendo en cuenta que el proceso de facturacion electronica ha experimentado multiples modificaciones, se han mantenido algunos campos de versiones anteriores los cuales no se veran reflejados en el xml final de envio, pero teniendo en cuenta que no se han descartado del todo, se mantienen en la presente estructura debido a su posible puesta en marcha en un futuro.

##### **Ejemplo de CABDOC**

```json

{
	"CABDOC": {
		"NITEMP": "123456789",
		"NUMRES": "98765432100",
		"TDOCU": "01",
		"NUMERO": "FACV100011337",
		"FECDOC": "2024-10-10",
		"FECVEN": "2024-10-10",
		"NOMVEN": "SOPORTE ADVANCIT",
		"MONEDA": "COP",
		"TIPCRU": "",
		"CODSUC": "1",
		"NUMREF": "",
		"FORIMP": "",
		"CLADET": "D",
		"FORPAG": "1",
		"ORDENC": "",
		"NUREMI": "",
		"NORECE": "",
		"EANTIE": "",
		"COMODE": "COP",
		"COMOHA": "",
		"FACCAL": "",
		"FETACA": "",
		"FECREF": "",
		"OBSREF": "",
		"OBSERV": "",
		"TEXDOC": "",
		"MODEDI": "",
		"NUMENT": "",
		"NDIAN": "FACV100011337",
		"SOCIED": "",
		"CODVEN": "200",
		"MOTDEV": "",
		"SUBTO": "5500.00",
		"BASIMP": "0.00",
		"TOTFAC": "5500.00",
		"TOTIMP": "0.00",
		"TOTDES": "0.00",
		"DIPAPA": "0",
		"TIPOPE": "SS-CUFE",
		"MEDPAG": "10",
		"CLAVE": "123456789",
		"USUAR": "ADVANCIT",
		"INPESD": "",
		"INPEST": "",
		"INPEND": "",
		"INPENT": "",
		"ANDREID": "",
		"ANDREDA": "",
		"ANDREDT": "",
		"CUNAMA": "",
		"CUVALA": "",
		"CUNAMB": "",
		"CUVALB": "",
		"CIGSHN": "",
		"CIGCSH": "",
		"IPTURD": "",
		"PREPID": 0,
		"PREPAM": "",
		"PREDRE": ""
	}
}

```

Este JSON debe codificarse en base64 antes de ser incluido en el valor correspondiente dentro del XML SOAP.


## Campos de Datos del Cliente (CLIDOC)
| Descripción                        | Nombre   | Ejemplo          | Especificación | Nota                                          | Oblig. |
|------------------------------------|----------|------------------|----------------|-----------------------------------------------|--------|
| Clase persona                      | CLAPER   | 1                | CHAR(1)        | Ver tabla Anexo-Tecnico-Factura-Electronica-de-Venta-vr-1-9.pdf (13.2.7.3 Tipo de organización (Personas Natural y Juridica): cbc:AdditionalAccountID)   | SI     |
| Código documento                   | CODDOC   | 31               | CHAR(2)        | Código de documento de identificación.           | SI     |
| Número de documento                | NUMDOC   | 900496336        | CHAR(20)       | No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Dígito de verificación             | DIVECL   | 2                | CHAR(1)        | Solo aplica si es código de documento 31.        | SI     |
| Código país                        | PAICLI   | CO               | CHAR(2)        | CO=Colombia Ver tabla Anexo-Tecnico-Factura-Electronica-de-Venta-vr-1-9.pdf (13.2.9.1 Países (ISO 3166-1): cbc:IdentificationCode)   | SI     |
| Departamento del cliente           | DEPCLI   | Santander        | CHAR(40)       | Ver tabla Anexo-Tecnico-Factura-Electronica-de-Venta-vr-1-9.pdf (13.2.9.2 Departamentos (ISO 3166-2:CO): cbc:CountrySubentity, cbc:CountrySubentityCode) No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Ciudad del cliente                 | CIUCLI   | San Gil          | CHAR(40)       | Ver tabla Anexo-Tecnico-Factura-Electronica-de-Venta-vr-1-9.pdf (13.2.9.3 Municipios: cbc:CityName) No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Barrio del cliente                 | LOCCLI   |                  | CHAR(40)       | Si requiere indicar el barrio del cliente.        | NO     |
| Dirección del cliente              | DIRCLI   | Kilómetro 1 Vía San Gil Charalá | CHAR(200) | No completar con nada ni a la izquierda ni a la derecha. | SI |
| Teléfono del cliente               | TELCLI   | 3208376408       | CHAR(200)       | No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Nombre y apellidos completos       | NOMCON   | Fyg S.a.s.       | CHAR(200)       | No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Régimen tributario                 | REGTRI   | 2                | CHAR(4)         | Tabla DIAN - Régimen Tributario (0 Simplificado, 2 Común)| SI     |
| Razón social cliente               | RAZSOC   | Fyg S.a.s.       | CHAR(200)       | No completar con nada ni a la izquierda ni a la derecha. | SI si código de documento 31 |
| Primer nombre                      | PNOMBR   |                  | CHAR(100)       | No completar con nada ni a la izquierda ni a la derecha. | SI si código de documento 13 |
| Segundo nombre                     | SNOMBR   |                  | CHAR(100)       | No completar con nada ni a la izquierda ni a la derecha. | SI si código de documento 13 |
| Apellidos                          | APELLI   |                  | CHAR(100)       | No completar con nada ni a la izquierda ni a la derecha. | SI si código de documento 13 |
| Mail de notificación               | MAICLI   | siac_frr@hotmail.com | CHAR(255)    | No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Número de matrícula                | NUMMAT   |                  | CHAR(150)       | Si requiere manejar número de matrícula.                  | NO     |
| Nombre país                        | NOMPAI   | Colombia         | CHAR(150)       | No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Código ciudad                      | CODCIU   | 68001            | CHAR(20)        | Fuente tabla DANE.                                        | SI     |
| Código departamento                | CODDEP   | 68               | CHAR(20)        | Fuente tabla DANE.                                        | SI     |
| Obligaciones o responsabilidades   | OBLCON   | O-09             | CHAR(200)       | Ver tabla Anexo-Tecnico-Factura-Electronica-de-Venta-vr-1-9.pdf (13.2.7.6 Responsabilidades fiscales)     | SI     |
| Identificador del registro	     | REGID    |                  | Alfanumérico | Código único que identifica el registro del cliente en el sistema. | Sí              |
| Nombre del registro		     | REGNAM   |                  | Texto        | Nombre o descripción oficial asociada al registro del cliente.      | Sí              |

NOTA: Teniendo en cuenta que el proceso de facturacion electronica ha experimentado multiples modificaciones, se han mantenido algunos campos de versiones anteriores los cuales no se veran reflejados en el xml final de envio, pero teniendo en cuenta que no se han descartado del todo, se mantienen en la presente estructura debido a su posible puesta en marcha en un futuro.

##### **Ejemplo de CLIDOC**

```json
{
	"CLIDOC": {
		"CLAPER": "1",
		"CODDOC": "31",
		"NUMDOC": "123456789",
		"DIVECL": "5",
		"PAICLI": "CO",
		"DEPCLI": "BOGOTA D.C.",
		"CIUCLI": "BOGOTA",
		"LOCCLI": "",
		"DIRCLI": "AVENIDA 45 N 67-123",
		"TELCLI": "3201234567",
		"NOMCON": "CLINICA DE PRUEBAS ADVANCIT",
		"REGTRI": "4",
		"RAZSOC": "CLINICA DE PRUEBAS ADVANCIT S.A.",
		"PNOMBR": "",
		"SNOMBR": "",
		"APELLI": "",
		"MAICLI": "contacto@advancit.com",
		"NUMMAT": "",
		"NOMPAI": "COLOMBIA",
		"CODCIU": "11001",
		"CODDEP": "11",
		"OBLCON": "O-15",
		"REGID": "",
		"REGNAM": ""

	}
}

```

Este JSON debe codificarse en base64 antes de ser incluido en el valor correspondiente dentro del XML SOAP.

## Campos de Detalle del Documento (DETDOC)
| Descripción                        | Nombre   | Ejemplo          | Especificación | Nota                                          | Oblig. |
|------------------------------------|----------|------------------|----------------|-----------------------------------------------|--------|
| Consecutivo detalle documento      | IDEPOS   | 1                | INT(6)          | Número Consecutivo.                                       | SI     |
| Código de producto                 | CODITE   | 1                | CHAR(40)        | No completar con nada ni a la izquierda ni a la derecha.  | SI     |
| Descripción del producto           | NOMITE   | Triturado De 1/2 Menor Calidad | CHAR(255) | No completar con nada ni a la izquierda ni a la derecha.  | SI     |
| Unidad de medida                   | UNIMED   | 94               | CHAR(5)         | Ver tabla Anexo-Tecnico-Factura-Electronica-de-Venta-vr-1-9.pdf (13.2.8.6 Unidades de Cantidad: @unitCode)    | SI     |
| Cantidad del producto              | CANITE   | 2.00             | DEC(8,2)        |                                                          | SI     |
| Valor unitario del producto        | VALITE   | 23109.00         | DEC(15,2)       | Valor sin impuestos y unitario del artículo.              | SI     |
| Valor total de los productos       | TOTVAD   | 46218.00         | DEC(15,2)       | Resultado de multiplicar VALITE * CANITE.                 | SI     |
| Valor del impuesto                 | VALIMD   | 8781.42          | DEC(15,2)       | Resultado de multiplicar TOTVAD * PORIMD.                 | SI     |
| Valor del descuento                | VALDED   | 0.00             | DEC(15,2)       |                                                          | NO     |
| Porcentaje del impuesto            | PORIMD   | 19.00            | DEC(15,2)       |                                                          | SI     |
| Código del impuesto                | CODIMD   | 01               | CHAR(2)         | Ver tabla Anexo-Tecnico-Factura-Electronica-de-Venta-vr-1-9.pdf (13.2.7.2 Tributos)  | SI     |
| Nombre del impuesto                | NOMIMD   | IVA              | CHAR(255)       | Ver tabla Anexo-Tecnico-Factura-Electronica-de-Venta-vr-1-9.pdf (13.2.7.2 Tributos)                     | SI     |
| Descuento aplicable Salud A        | DESSAA   |       	   | CHAR(255)       |                     	| NO     |
| Descuento aplicable Salud B        | DESSAB   | 	           | CHAR(255)       |                    	| NO     |



##### **Ejemplo de DETDOC**

```json
{
	"DETDOC": [
		{
			"IDEPOS": 1,
			"CODITE": "123456",
			"NOMITE": "RESONANCIA MAGNETICA CEREBRAL",
			"UNIMED": "",
			"CANITE": "1.00",
			"VALITE": "300000.00",
			"TOTVAD": "264000.00",
			"VALIMD": "0.00",
			"VALDED": "36000.00",
			"PORIMD": "0.00",
			"CODIMD": "ZY",
			"NOMIMD": "No causa",
			"DESSAA": "",
			"DESSAB": ""
		},
		{
			"IDEPOS": 2,
			"CODITE": "654321",
			"NOMITE": "CONSULTA GENERAL DE PRIMERA VEZ",
			"UNIMED": "",
			"CANITE": "1.00",
			"VALITE": "85000.00",
			"TOTVAD": "74560.00",
			"VALIMD": "0.00",
			"VALDED": "10440.00",
			"PORIMD": "0.00",
			"CODIMD": "ZY",
			"NOMIMD": "No causa",
			"DESSAA": "",
			"DESSAB": ""
		},
		{
			"IDEPOS": 3,
			"CODITE": "789012",
			"NOMITE": "EXAMEN DE SANGRE COMPLETO",
			"UNIMED": "",
			"CANITE": "1.00",
			"VALITE": "45000.00",
			"TOTVAD": "39560.00",
			"VALIMD": "0.00",
			"VALDED": "5440.00",
			"PORIMD": "0.00",
			"CODIMD": "ZY",
			"NOMIMD": "No causa",
			"DESSAA": "",
			"DESSAB": ""
		}
	]
}

```

Este JSON debe codificarse en base64 antes de ser incluido en el valor correspondiente dentro del XML SOAP.


## Campos de Impuestos (DETIMPDOC)

| Descripción                        | Nombre   | Ejemplo          | Especificación | Nota                                          | Oblig. |
|------------------------------------|----------|------------------|----------------|-----------------------------------------------|--------|
| Consecutivo impuestos documento    | IDEIMP   | 1                | INT(6)          | Número Consecutivo.                                       | SI     |
| Código impuesto                    | CODIMP   | 01               | CHAR(2)         | Ver tabla Anexo-Tecnico-Factura-Electronica-de-Venta-vr-1-9.pdf (13.2.7.2 Tributos)   | SI     |
| Nombre del impuesto                | NOMIMP   | IVA              | CHAR(255)       | Ver tabla Anexo-Tecnico-Factura-Electronica-de-Venta-vr-1-9.pdf (13.2.7.2 Tributos)   | SI     |
| Porcentaje impuesto                | PORIMP   | 19.00            | DEC(15,2)       |                                                          | SI     |
| Valor del impuesto                 | VALIMP   | 8781.42          | DEC(15,2)       | Valor impuesto resultado de multiplicar BASIMP * PORIMP.  | SI     |
| Base gravable impuesto             | BASIMP   | 46218.00         | DEC(15,2)       | Valor sobre el cual se calculan los impuestos.            | SI     |

##### **Ejemplo de DETIMP**

```json
{
	"DETIMPDOC": [
		{
			"IDEIMP": 1,
			"CODIMP": "ZY",
			"NOMIMP": "No causa",
			"PORIMP": "0.00",
			"VALIMP": "0.00",
			"BASIMP": "0.00"
		}
	]
}
```

Este JSON debe codificarse en base64 antes de ser incluido en el valor correspondiente dentro del XML SOAP.


## Campos de Detalle de Descuento (DESCDOC)
| Descripción                        | Nombre   | Ejemplo          | Especificación  | Nota                                                    | Oblig. |
|------------------------------------|----------|------------------|-----------------|---------------------------------------------------------|--------|
| Consecutivo de descuento           		| CONDES   |                  | INT(6)          | Número Consecutivo del descuento.                        | SI     |
| Código de descuento                		| CODDES   |                  | CHAR(2)         | Ver tabla Anexo-Tecnico-Factura-Electronica-de-Venta-vr-1-9.pdf (13.2.8.10 Tablas de tarifas por Impuesto)   | SI     |
| Porcentaje de descuento            		| PORDES   |                  | DEC(15,2)       |                                                          | SI     	|
| Valor de descuento                 		| VALDES   |                  | DEC(15,2)       |                                                          | SI     	|
| Base sobre la cual se aplica el descuento 	| VABADE   |                  | DEC(15,2)       |                                                          | SI     	|
| Texto abierto del descuento        		| TEXDES   |                  | CHAR(255)       |                                                          | SI     	|
| Detalle del descuento asignado A       	| DESSAA   |                  | Texto		| Descripción específica sobre el descuento tipo A. 	   | No    	|
| Detalle del descuento asignado B       	| DESSAB   |                  | Texto        	| Descripción específica sobre el descuento tipo B. 	   | No		|


##### **Ejemplo de DESCDOC**

```json
{
	"DESCDOC": [
		{
			"CONDES": "",
			"CODDES": "",
			"PORDES": "",
			"VALDES": "",
			"VABADE": "",
			"TEXDES": "",
			"DESSAA": "",
			"DESSAB": ""

		}
	]
}
```

Este JSON debe codificarse en base64 antes de ser incluido en el valor correspondiente dentro del XML SOAP.


## Campos Adicionales Documento (ADIDOC)
| Descripción                        | Nombre   | Ejemplo          | Especificación  | Nota                                                    | Oblig. |
|------------------------------------|----------|------------------|-----------------|---------------------------------------------------------|--------|
| Identificador adicional            | IDEADD   | 1                | CHAR(6)         | Identificador adicional del documento.                    | SI     |
| Descripción adicional              | TEXADD   | Información adicional | CHAR(100)  | Descripción adicional del documento.                    | SI     |
| Valor adicional                    | VALNDD   | 1000.00          | DEC(15,2)       | Valor adicional en números.                              | NO     |
| Valor adicional en texto           | VALTDD   | Mil Pesos        | CHAR(255)       | Valor adicional en texto.                                | NO     |


##### **Ejemplo de ADIDOC**

```json
{
	"ADIDOC": [
		{
			"IDEADD": "1",
			"TEXADD": "",
			"VALNDD": "",
			"VALTDD": ""
		}
	]
}
```

Este JSON debe codificarse en base64 antes de ser incluido en el valor correspondiente dentro del XML SOAP.


## Campos de Detalle del Producto (DETALLEPRO)
| Descripción                        | Nombre   | Ejemplo          | Especificación  | Nota                                                    | Oblig. |
|------------------------------------|----------|------------------|-----------------|---------------------------------------------------------|--------|
| Consecutivo de detalle de productos | IDEPOS   | 1                | INT(6)          | Consecutivo de productos en el documento.                | SI     |
| Código de producto                 | CODITE   | 123456789        | CHAR(40)        | Código del producto en el detalle.                       | SI     |
| Código de posición del producto    | COPODP   | 01               | CHAR(2)         | Código de la posición del producto.                      | NO     |
| Nombre de la posición del producto | NOPODP   | Producto A       | CHAR(200)       | Nombre asignado a la posición del producto.              | NO     |
| Valor del producto                 | VAPODP   | 10000.00         | DEC(15,2)       | Valor del producto.                                      | SI     |
| Porcentaje del producto            | POPODP   | 19.00            | DEC(15,2)       | Porcentaje aplicado al producto.                         | SI     |

##### **Ejemplo de DETALLEPRO**

```json
{
	"DETALLEPRO": [
		{
			"IDEPOS": 1,
			"CODITE": "123456",
			"COPODP": "ZY",
			"NOPODP": "No causa",
			"VAPODP": "264000.00",
			"POPODP": "0.00"
		},
		{
			"IDEPOS": 2,
			"CODITE": "654321",
			"COPODP": "ZY",
			"NOPODP": "No causa",
			"VAPODP": "74560.00",
			"POPODP": "0.00"
		},
		{
			"IDEPOS": 3,
			"CODITE": "789012",
			"COPODP": "ZY",
			"NOPODP": "No causa",
			"VAPODP": "39560.00",
			"POPODP": "0.00"
		}
	]
}

```

Este JSON debe codificarse en base64 antes de ser incluido en el valor correspondiente dentro del XML SOAP.

## Campos de Datos de Salud (DATSAL)

Para el sector salud, se ha definido un arreglo que contiene información adicional requerida, como el código del prestador, la modalidad de pago, la cobertura del plan de beneficios, entre otros. Este arreglo incluye objetos con dos campos principales: 

- **DASNAM**: Nombre del dato de salud.
- **DASVAL**: Valor correspondiente al dato.

Los objetos que conforman este arreglo están descritos en la siguiente tabla:

| **Descripción**                  | **Nombre**                   | **Ejemplo**           | **Especificación** | **Nota**                                                | **Obligatorio** |
|----------------------------------|------------------------------|-----------------------|--------------------|---------------------------------------------------------|-----------------|
| Código del prestador             | `CODIGO_PRESTADOR`                    | `6541324642159`       | varchar(150)               | Identificador único del prestador de servicios.         | Sí              |
| Modalidad de pago                | `MODALIDAD_PAGO`            | `11`                 | varchar(150)           | Ver tabla LINEAMIENTOS PARA LA GENERACIÓN, VALIDACIÓN Y ENVÍO DEL REGISTRO INDIVIDUAL DE PRESTACION DE SERVICIOS DE SALUD – RIPS, COMO SOPORTE DE LA FACTURA ELECTRÓNICA DE VENTA – FEV EN SALUD (Tabla 2: Accesos directos a las tablas de FEV-RIPS - TB 38)                    | Sí              |
| Cobertura del plan de beneficios | `COBERTURA_PLAN_BENEFICIOS` | `01`                 | varchar(150)	     |  Ver tabla LINEAMIENTOS PARA LA GENERACIÓN, VALIDACIÓN Y ENVÍO DEL REGISTRO INDIVIDUAL DE PRESTACION DE SERVICIOS DE SALUD – RIPS, COMO SOPORTE DE LA FACTURA ELECTRÓNICA DE VENTA – FEV EN SALUD (Tabla 2: Accesos directos a las tablas de FEV-RIPS - TB 36) | No              |
| Número del contrato              | `NUMERO_CONTRATO`           | `632656-5416`         | varchar(150)          | Referencia única del contrato del servicio.             | No              |
| Número de póliza                 | `NUMERO_POLIZA`             | `10000.00`            | varchar(150)          | Monto asociado a la póliza del beneficiario.            | Sí              |

---

### Ejemplo de estructura JSON

A continuación, se presenta un ejemplo de cómo estructurar los datos en formato JSON:

```json
{
  "DATSAL": [
    {
      "DASNAM": "CODIGO_PRESTADOR",
      "DASVAL": "6541324642159"
    },
    {
      "DASNAM": "MODALIDAD_PAGO",
      "DASVAL": "11"
    },
    {
      "DASNAM": "COBERTURA_PLAN_BENEFICIOS",
      "DASVAL": "01"
    },
    {
      "DASNAM": "NUMERO_CONTRATO",
      "DASVAL": "632656-5416"
    },
    {
      "DASNAM": "NUMERO_POLIZA",
      "DASVAL": "656565"
    }
  ]
}
```
### Notas importantes:
1. Este JSON debe codificarse en **Base64** antes de ser incluido en el valor correspondiente dentro del XML SOAP.
2. Todos los campos obligatorios deben estar presentes para garantizar el cumplimiento de las especificaciones.


## Campos de Pago Previo (PREPAI)
| Descripción                        | Nombre   | Ejemplo          | Especificación  | Nota                                                    | Oblig. |
|------------------------------------|----------|------------------|-----------------|---------------------------------------------------------|--------|
| Código del tipo de pago            | PRECOD   | 01               | varchar(150)         | Código del tipo de pago (ej. "01" para copago).          | SI     |
| Nombre del tipo de pago            | PRENOM   | Copago           | varchar(150)        | Nombre del tipo de pago.                                 | SI     |
| Valor del pago                     | PREVAL   | 6900.00          | varchar(150)       | Valor del pago realizado.                                | SI     |
| Fecha del pago                     | PREFEC   | 2024-09-06       | varchar(150)            | Fecha en que se realizó el pago.                         | SI     |

##### **Ejemplo de PREPAI**

```json
{
	"PREPAI": [
		{
			"PRECOD": "01",
			"PRENOM": "Copago",
			"PREVAL": "6900.00",
			"PREFEC": "2024-09-06"
		}
	]
}
```

Este JSON debe codificarse en base64 antes de ser incluido en el valor correspondiente dentro del XML SOAP.



## Campos de Datos de Salud - Información de Archivos (PURSAL)

En esta sección se detalla la estructura para los datos asociados a archivos relevantes dentro del proceso de facturación electrónica en el sector salud. 

### Notas importantes:
1. Todos los campos de este JSON son **obligatorios** y deben estar correctamente diligenciados.
2. Los valores deben seguir el formato especificado en la tabla para garantizar su validez.
3. Este JSON se debe incluir en la solicitud en el formato indicado, codificado en **Base64** antes de su inserción en el XML SOAP.

### Estructura de los campos:

| **Descripción**                  | **Nombre**   | **Ejemplo**       | **Especificación** | **Nota**                                            | **Obligatorio** |
|----------------------------------|--------------|-------------------|--------------------|---------------------------------------------------|-----------------|
| Nombre del archivo               | `PAGNAM`     | `excelFile`       | CHAR               | Identificador del archivo relacionado.            | Sí              |
| Valor del archivo (nombre real)  | `PAGVAL`     | `a1b2c3.xlsx`     | CHAR               | Nombre del archivo con su extensión.             | Sí              |

---

### Ejemplo de estructura JSON

```json
{
  "PURSAL": [
    {
      "PAGNAM": "excelFile",
      "PAGVAL": "a1b2c3.xlsx"
    }
  ]
}
```

### Instrucciones adicionales:
1. Este JSON debe codificarse en **Base64** antes de ser incluido en el XML SOAP correspondiente.
2. Asegúrate de que todos los campos cumplen con las especificaciones para evitar errores en el procesamiento.

## Campos de Datos de Salud - Información Adicional de Archivos (PUESAL)

Esta sección describe la estructura de los datos relacionados con archivos adicionales que deben incluirse en los procesos de facturación electrónica para el sector salud.  

### Notas importantes:
1. Los campos de este JSON son **obligatorios** y deben llenarse de acuerdo con las especificaciones indicadas.
2. Es fundamental garantizar que todos los valores cumplan con los formatos esperados para evitar errores durante el procesamiento.
3. Este JSON debe ser codificado en **Base64** antes de su incorporación al XML SOAP.

### Estructura de los campos:

| **Descripción**                  | **Nombre**   | **Ejemplo**       | **Especificación** | **Nota**                                            | **Obligatorio** |
|----------------------------------|--------------|-------------------|--------------------|---------------------------------------------------|-----------------|
| Nombre del archivo               | `PAGNAM`     | `excelFile`       | CHAR               | Identificador descriptivo del archivo adjunto.    | Sí              |
| Valor del archivo (nombre real)  | `PAGVAL`     | `a1b2c3.xlsx`     | CHAR               | Nombre del archivo incluyendo su extensión.       | Sí              |

---

### Ejemplo de estructura JSON

```json
{
  "PUESAL": [
    {
      "PAGNAM": "excelFile",
      "PAGVAL": "a1b2c3.xlsx"
    }
  ]
}
```

### Instrucciones adicionales:
1. El JSON debe codificarse en **Base64** para su inclusión en el XML SOAP correspondiente.
2. Verifica que los nombres de los archivos sean únicos y descriptivos para facilitar la identificación.


### **Paso 3: Envío de la Petición**
Una vez construida la petición, se puede enviar utilizando una librería SOAP en PHP, por ejemplo:

```php
$client = new SoapClient('http://mff.advancit.co/facturaelectronicav2/ws_electronic_invoice_basic.wsdl', array('trace' => 1));
$argsen = array('secado' => $B64secado, 'secldo' => $B64secldo);  // Agregar más parámetros codificados en base64
$resins = $client->__soapCall('send_document', array('sendoc' => $argsen));
```

### **Paso 4: Manejo de Respuestas**
La respuesta de la DIAN puede incluir la aceptación o rechazo de la factura enviada, junto con los errores detectados. Es importante manejar y registrar tanto la solicitud como la respuesta para futuras referencias.

### **Paso 5: Manejo de Errores**
Si ocurre un error, es necesario manejar las excepciones de SOAP y analizar el contenido de las respuestas de error para corregir posibles problemas.

## 4. **Validaciones y Errores Comunes**
### **Validaciones antes de enviar la petición**
- Verifica que los campos obligatorios en el JSON estén correctamente formateados y completos (por ejemplo, NIT, número de resolución, tipo de documento, etc.).
- Asegúrate de que los valores numéricos, como totales e impuestos, coincidan.

### **Manejo de errores comunes**
- **Errores de formato**: Asegúrate de que los campos obligatorios estén presentes y de que los valores numéricos estén correctamente calculados.
- **Errores de conexión**: Verifica la conectividad con el servicio y que la URL del WSDL esté accesible.

## 5. **Relación entre las Cabeceras del XML y los Datos JSON**

El cuerpo de la petición SOAP incluye un bloque `<sendoc>` que contiene varios ítems donde cada uno tiene una clave (`key`) 
en el XML y un valor (`value`) que corresponde a un conjunto de datos JSON codificado en BASE64. 
A continuación, se describe la relación entre los nombres de las cabeceras XML y los datos JSON:

#### **Formato General de la Relación XML-JSON**
Cada bloque en el XML tiene la siguiente estructura:
```xml
<item>
    <key xsi:type="xsd:string">[nombre_campo_XML]</key>
    <value xsi:type="xsd:string">{JSON codificado}</value>
</item>
```
Donde `[nombre_campo_XML]` hace referencia a la sección específica del JSON que contiene la información relevante.

| **Nombre en XML** | **Nombre en JSON** | **Descripción**                                      |
|-------------------|--------------------|------------------------------------------------------|
| `secado`          | `CABDOC`           | Información de la cabecera del documento (factura)   |
| `secldo`          | `CLIDOC`           | Información del cliente                              |
| `sededo`          | `DETDOC`           | Detalle de los productos o ítems del documento       |
| `sedepr`          | `DETALLEPRO`       | Detalle del producto adicional                       |
| `sedeim`          | `DETIMPDOC`        | Detalle de los impuestos aplicados                   |
| `seaddo`          | `ADIDOC`           | Información adicional del documento                  |
| `sedaws`          | `DATOEMP`          | Datos de la empresa que emite el documento           |
| `sedecu`          | `DESCDOC`          | Descuento del Documento				|
| `datsal`          | `DATSAL`           | Datos Sector Salud					|
| `prepai`          | `PREPAI`           | Información sobre el pago previo sector salud	|
| `pursal`          | `PURSAL`           | Datos relacionados con archivos                     	|
| `puesal`          | `PUESAL`           | Datos relacionados con archivos adicionales          |


## 6. **Ejemplos Prácticos**

### **Ejemplo completo de petición SOAP**
```xml

<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope 
    SOAP-ENV:encodingStyle="http://schemas.xmlsoap.org/soap/encoding/" 
    xmlns:SOAP-ENC="http://schemas.xmlsoap.org/soap/encoding/" 
    xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" 
    xmlns:ns1="urn:documents" 
    xmlns:ns2="http://xml.apache.org/xml-soap" 
    xmlns:xsd="http://www.w3.org/2001/XMLSchema" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <SOAP-ENV:Body>
    <ns1:send_document>
      <sendoc xsi:type="ns2:Map">
        <item>
          <key xsi:type="xsd:string">secado</key>
          <value xsi:type="xsd:string">{JSON CABDOC codificado en BASE64}</value>
        </item>
        <item>
          <key xsi:type="xsd:string">secldo</key>
          <value xsi:type="xsd:string">{JSON CLIDOC codificado en BASE64}</value>
        </item>
        <item>
          <key xsi:type="xsd:string">sededo</key>
          <value xsi:type="xsd:string">{JSON DETDOC codificado en BASE64}</value>
        </item>
        <item>
          <key xsi:type="xsd:string">sedepr</key>
          <value xsi:type="xsd:string">{JSON DETALLEPRO codificado en BASE64}</value>
        </item>
        <item>
          <key xsi:type="xsd:string">sedeim</key>
          <value xsi:type="xsd:string">{JSON DETIMP codificado en BASE64}</value>
        </item>
        <item>
          <key xsi:type="xsd:string">seaddo</key>
          <value xsi:type="xsd:string">{JSON SEADDO codificado en BASE64}</value>
        </item>
        <item>
          <key xsi:type="xsd:string">sedaws</key>
          <value xsi:type="xsd:string">{JSON DATOEMP codificado en BASE64}</value>
        </item>
        <item>
          <key xsi:type="xsd:string">sedecu</key>
          <value xsi:type="xsd:string">{JSON DESCDOC codificado en BASE64}</value>
        </item>
        <item>
          <key xsi:type="xsd:string">datsal</key>
          <value xsi:type="xsd:string">{JSON DATSAL codificado en BASE64}</value>
        </item>
        <item>
          <key xsi:type="xsd:string">seepai</key>
          <value xsi:type="xsd:string">{JSON PREPAI codificado en BASE64}</value>
        </item>
        <item>
          <key xsi:type="xsd:string">pursal</key>
          <value xsi:type="xsd:string">{JSON PURSAL codificado en BASE64}</value>
        </item>
        <item>
          <key xsi:type="xsd:string">puesal</key>
          <value xsi:type="xsd:string">{JSON PUESAL codificado en BASE64}</value>
        </item>
      </sendoc>
    </ns1:send_document>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>

```

**Nota:** Los json encriptados en BASE64 no deben ir contenidos entre las llaves cuando nos refereimos a {JSON DATOEMP codificado en BASE64} dentro del valor del item.
En este ejemplo, cada campo dentro del XML (`secado`, `secldo`, `sededo`, etc.) hace referencia a un bloque JSON específico que contiene datos codificados en BASE64. Es importante asegurarse de que todos los datos estén correctamente formateados y codificados antes de su envío.

---

### **7. Escenarios de Envío**

Existen 4 tipos de envíos que determinan los campos obligatorios dependiendo del tipo de operación. Los escenarios son:

1. **SS Recaudo**: Archivo `SSCUFE_PN_Comentado`.
2. **SS-SinAporte**: Archivo `IPSA ERP_Comentado`.
3. **Acreditación**: Archivo `SSCUFE_IPS A EPS_Comentado`.
4. **No Acreditación**: Archivo `SS-Reporte_SIN RECAU_Comentado`.

Los archivos están disponibles en el directorio del repositorio.

---

### **8. Documentación Relacionada**

Para cumplir con los requisitos técnicos y normativos de la facturación electrónica en Colombia, es importante consultar la documentación oficial proporcionada por la DIAN. A continuación, se incluyen enlaces a los documentos clave y herramientas que son referenciados a lo largo de este manual:

#### **8.1. Documentación Oficial de la DIAN**
Este documento detalla las especificaciones técnicas para la implementación de la facturación electrónica de acuerdo con las normativas vigentes en Colombia:

- [Anexo Técnico Factura Electrónica de Venta - Versión 1.9](https://www.dian.gov.co/impuestos/factura-electronica/Documents/Anexo-Tecnico-Factura-Electronica-de-Venta-vr-1-9.pdf)

#### **8.2. Caja de Herramientas Técnica**
La DIAN proporciona una serie de recursos y herramientas técnicas que apoyan el proceso de integración de la facturación electrónica. Estos recursos incluyen documentación técnica adicional, ejemplos, y herramientas de validación:

- [Caja de Herramientas Técnica - Sistema de Facturación Electrónica](https://micrositios.dian.gov.co/sistema-de-facturacion-electronica/documentacion-tecnica/)

#### **8.3. Documentos Clave para el Sector Salud**

- [Resolución 1884 de 2024: Un Vistazo a los Cambios en el Sector Salud Colombiano](https://www.minsalud.gov.co/Normatividad_Nuevo/Resoluci%C3%B3n%20No%201884%20de%202024.pdf)
- [Modificaciones Resolución 636 de 2024: Factura RIPS](https://www.minsalud.gov.co/Normatividad_Nuevo/Resoluci%C3%B3n%20No%20636%20de%202024.pdf)
- [LINEAMIENTOS PARA LA GENERACIÓN, VALIDACIÓN Y ENVÍO DEL REGISTRO INDIVIDUAL DE PRESTACION DE SERVICIOS DE SALUD – RIPS, COMO SOPORTE DE LA FACTURA ELECTRÓNICA DE VENTA – FEV EN SALUD](https://www.minsalud.gov.co/sites/rid/Lists/BibliotecaDigital/RIDE/DE/OT/lineamientos-generacion-validacion-rips-factura-electronica-fev-doc-electronicos.pdf)

---
