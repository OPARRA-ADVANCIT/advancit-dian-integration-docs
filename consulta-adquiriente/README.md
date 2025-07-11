# Consulta de Adquiriente - WebService ADVANCIT

Este WebService permite a nuestros aliados consultar directamente con la DIAN la información básica (nombre y correo electrónico) de un adquiriente registrado en el sistema, a partir de su tipo y número de documento de identificación.

---

## 🧾 ¿Para qué sirve este servicio?

Este servicio es útil para validar datos de terceros antes de generar documentos electrónicos como facturas, documentos equivalentes o nómina electrónica. Al usarlo, puedes verificar que la información registrada en la DIAN coincida con la proporcionada por el usuario o cliente.

---

## 🔗 Endpoint (WSDL)

```
http://conector.advancit.co/ws_dian_produccion/ws_send_dian.php?wsdl
```

---

## 🛠️ Método SOAP: `cons_client`

Este método debe ser consumido bajo el protocolo SOAP y requiere el envío de un bloque `sendoc` con los siguientes campos:

### 🔐 Parámetros requeridos (Request)

| Campo   | Descripción |
|---------|-------------|
| `nitemp` | NIT de la empresa que realiza la consulta (tu empresa como aliado ADVANCIT). |
| `certif` | Nombre del archivo del certificado de firma electrónica usado para habilitación. Ej: `Certificado.p12`. |
| `pascer` | Contraseña del certificado de firma electrónica. |
| `tipide` | Tipo de documento del adquiriente a consultar. Corresponde a la tabla de referencia `13.2.1` del Anexo Técnico 1.9 de la DIAN (Tipo de Identificador Fiscal). Ej: `31` para NIT, `13` para Cédula de Ciudadanía, etc. |
| `numide` | Número del documento de identificación del adquiriente. |

> 📌 El tipo de documento (`tipide`) debe coincidir con el código definido por la DIAN. Puedes consultarlo en la [caja de herramientas del Anexo Técnico 1.9](https://www.dian.gov.co/).

---

## 📤 Ejemplo de Request

[Ver archivo `request-example.xml`](./request-example.xml)

---

## 📥 Ejemplo de Response

[Ver archivo `response-example.xml`](./response-example.xml)

Respuesta exitosa:
```json
{
  "RESDIA": {
    "maiadq": "gerencia@advancit.co",
    "nomadq": "ADVANCED INFORMATION TECHNOLOGIES S.A.S"
  }
}
```

### Campos del response

| Campo    | Descripción |
|----------|-------------|
| `maiadq` | Correo electrónico del adquiriente registrado ante la DIAN. |
| `nomadq` | Nombre o razón social del adquiriente. |

---

## ❌ Posibles errores comunes

| Código | Descripción |
|--------|-------------|
| `500 Internal Server Error` | Error al procesar los datos enviados o problemas con el certificado. |
| `401 Unauthorized` | Certificado inválido o credenciales incorrectas. |
| `400 Bad Request` | Algún campo obligatorio no fue enviado o no es válido. |

---

## 📞 Soporte

Si presentas inconvenientes con este servicio, puedes comunicarte con el equipo de soporte técnico a través de:  
📧 **soporte@advancit.co**
