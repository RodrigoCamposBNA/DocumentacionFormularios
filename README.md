# Generación automática de formularios mediante el uso de archivos JSON

## Objetivos

- Automatizar la generación de formularios de una complejidad media  mediante el uso de archivos Json que funcionan como un "archivo de creación/configuración" de cada formulario
- Automatizar las validaciones asociadas a c/u de los campos utilizando las facilidades de JavaScript Validation API ([Mas Info](https://developer.mozilla.org/en-US/docs/Web/API/Constraint_validation))
- Optimizar el pasaje a otros ambientes, evitando tener que pasar DLLs. Solo se pasan archivos estaticos 
- Agilizar el proceso de testing (si una validacion funciona en un formulario funciona en todos)

## Partes del formulario

## 1. title

### ***Descripción***

- Permite definir el titulo principal del formulario

### ***Ejemplo de Uso***

~~~
{
  "title": "ExpoAgro 2022 - Formulario 'Exclusivo Fabricantes'"
}
~~~

## 2. titleFooter

### ***Descripción***

- Permite especificar una pequeño parrafo por debajo del titulo

### ***Ejemplo de Uso***

~~~
{
  "titleFooter": "Antes de empezar a completar el formulario, leer atentamente las aclaraciones que se encuentran en la parte inferior del mismo."
}
~~~

## 3. image

### ***Descripción***

- Permite definir el slide del formulario
- Sino se especifica, se pone una imagen por defecto

### ***Ejemplo de Uso***

~~~
{
  "image": "expoagro2022.jpg"
}
~~~

## 4. subtitle

### ***Descripción***

- Permite ingresar un subtitulo al comienzo del formulario

### ***Ejemplo de Uso***

~~~
{
  "subtitle": "Datos Empresa"
}
~~~

## 5. active

### ***Descripción***

- Valor booleano que permite habilitar/deshabilitar un formulario

### ***Ejemplo de Uso***

~~~
{
  "active": true
}
~~~

## 6. legales

### ***Descripción***

- Permite ingresar un parrafo con los legales asociados al producto que se esta ofreciendo (letra chica)
- No se permite la modificacion de la fuente utilizada

### ***Ejemplo de Uso***

~~~
 {
  "legales": "Todos los campos del formulario son obligatorios. \n \n El campo Razón Social debe ser consistente con CUIT en el caso de Empresas.\n \n En el caso de presentarse inconsistencias o se incluyan en campos del formulario destinados al cliente con datos de Concesionarios y/o Fabricantes en reemplazo de los correspondientes al cliente Empresa, la solicitud será anulada.\n \n Los campos 'Producto a Comprar' y 'Valor de Referencia en Pesos', serán considerados por el Banco sólo a modo orientativo, por lo cual podrá ser integrado en forma aproximada. Solo se podrá incluir un bien, aunque el cliente pueda financiar más de uno a posteriori.\n \n Los datos que se ingresen en Contacto Empresa, refiere a la persona con la cual se iniciará el contacto/relación con el banco. Los datos que se ingresen en el campo Mail Empresa Fabricante, refiere a la dirección de correo del Fabricante que está realizando la carga.\n \n Los campos incluidos en el apartado 'Sucursal' están destinado a seleccionar la Sucursal con la cual opera la Empresa en el caso de ser cliente del BNA, ó en su defecto, en la cual desea operar. Para este último caso sugerimos seleccionar la más cercana al domicilio de la Empresa.\n \n Una vez procesado el formulario, el visitante recibirá en el correo electrónico consignado, una invitación del Fabricante a concurrir, ya sea al Stand del Banco en ExpoAgro 2022 o a la sucursal seleccionada del BNA a partir del día 08-03-2022, a los efectos de conocer los beneficio de la promoción en la Exposición.\n \n IMPORTANTE: SOLO SERÁ VALIDA UNA SOLICITUD POR CLIENTE LA CUAL PUEDE SER UTILIZADA PARA LA COMPRA DE MÁS DE UN BIEN."
}
~~~

## 7. post

### ***Descripción***

- Define la clausula que permite insertar el registro en BD
- Si no se define este tag, se ejecutara el circuito en modo "Test" (se ejecuta todo el cicuito sin insertar en BD)
- El tag "confirmationMessage" define el mensaje que se muestra cuando la solicitud se procesa de manera satisfactoria


### ***Ejemplo de Uso***

~~~
"post":{
    "sql": "INSERT INTO FrmExpo2022Agro(RazonSocial,Nombre,Apellido,NroCuit,CorreoElectronico,Telefono,Fabricante,CorreoElecronicoFabricante,ProductoAComprar,MontoSolicitado,Sucursal,FechaCreacion,TipoConsulta,CorreoElectronicoContacto,TelefonoContacto) VALUES (@RazonSocial,@Nombre,@Apellido,@NroCuit,@CorreoElectronico,@Telefono,@Fabricante,@CorreoElecronicoFabricante,@ProductoAComprar,@MontoSolicitado,@Sucursal,GETDATE(),'Empresa',@CorreoElectronicoContacto,@TelefonoContacto)",
    "confirmationMessage" : "Hemos recibido la solicitud asociada al producto ExpoAgro"
  }
~~~

## 8. unique

### ***Descripción***

- Define la clausula con la que se validara si una solicitud esta repetida
- El tag "isNotValidMessage" define el mensaje que se muestra cuando el cliente ya tiene una solicitud asociada

### ***Ejemplo de Uso***

~~~
 "unique": {
    "sql": "SELECT COUNT(*) FROM FrmExpo2022Agro WHERE NroCuit = @nroCuit",
    "isNotValidMessage" : "Verificamos que ya generaste una solicitud. En caso que necesites ayuda, comunicate con nuestro Centro de Contactos al 0810-666-4444"
  }
~~~

## 9. count

### ***Descripción***

- Define el tope maximo de solicitudes que pueden ser cargadas en la BD para el producto asociado al formulario

- El tag "sql" representa la sentencia SQL que contabiliza la cantidad de registros insertados en la BD para el producto asociado al formulario

- La cantidad que arroja la consulta SQL asociada al tag "sql" no puede ser mayor al numero especificado en el tag "limit"

- El tag "isNotValidMessage" define el mensaje que se muestra cuando se llega al tope maximo de solicitudes

### ***Ejemplo de Uso***

~~~
"count" :{
    "limit" : 2,
    "sql" : "SELECT COUNT(*) FROM FrmExpo2022Agro",    
    "isNotValidMessage" : "CANTIDADES - Se ha alcanzado la totalidad de cupos disponibles asociados a ExpoAgro"
  }
~~~

## 10. sum

### ***Descripción***

- Define el monto maximo de dinero que el banco asigna al producto asociado al formulario. 

- El tag "sql" representa la sentencia SQL que sumariza el importe otorgado a cada cliente para el producto asociado al formulario

- La suma que arroja la consulta SQL asociada al tag "sql" no puede ser mayor al monto especificado en el tag "limit"

- El tag "isNotValidMessage" define el mensaje que se muestra cuando se llega al monto maximo de dinero que el banco asigna al producto asociado al formulario


### ***Ejemplo de Uso***

~~~
"sum" :{
    "limit" : 600000.00,
    "sql" : "SELECT SUM(MontoSolicitado) FROM FrmExpo2022Agro;",    
    "isNotValidMessage" : "SUMA - Se ha alcanzado la totalidad de cupos disponibles asociados a ExpoAgro"
  }
~~~

## 11. get

### ***Descripción***

- Define la clausula que permite traer datos precargados de la BD
- Los datos precargados apareceran grisados en el formulario

### ***Ejemplo de Uso***

~~~
"get":{
      "sql": "SELECT a.Apellido,a.Nombre,a.cuilt,a.documento, Monto AS montoPreacordado FROM AdhesionYBajaProducto_identificacion AS a INNER JOIN PrecalificadosGarrahan AS p ON a.cuilt = p.Cuil WHERE a.id = @id AND a.code = @code AND a.codigoProducto = 'Garrahan'"
    }
~~~

## 12. fechaVigencia

### ***Descripción***

- Define la franja de dias en la que el formulario estara activo
- El tag "isNotValidMessage" define el mensaje que se muestra cuando el formulario se carga en una franja de dias no valida


### ***Ejemplo de Uso***

~~~
 "fechaVigencia" : {
    "start" : "01/02/2022",
    "end" : "31/03/2022",
    "isNotValidMessage" : "El formulario no se encuentra habilitado"
  }
~~~

## 13. franjaHoraria

### ***Descripción***

- Define la franja horaria en la que el formulario estara activo
- El tag "isNotValidMessage" define el mensaje que se muestra cuando el formulario se carga en una franja horaria no valida

### ***Ejemplo de Uso***

~~~
 "franjaHoraria" : {
    "start" : "08:00",
    "end" : "22:00",
    "isNotValidMessage" : "El formulario no se encuentra disponible es este horario."
  }
~~~

## 14. sendEmails

### ***Descripción***

- Define los mails que se enviaran luego de que el formulario se procese de manera satisfactoria
- Pueden enviarse tantos mails como se deseen
- El tag "receiver" define el destinatorio del correo. Permite ingresar una referencia a un control del formulario o una casilla harcodeada


### ***Ejemplo de Uso***

~~~
"sendEmails" : [
      {
          "receiver" : "CorreoElectronico",
          "title" : "BNA - Expoagro 2023",
          "body" : {
            "content" : "<p>Estimad@ {0} {1}</p><p>Desde el Banco Naci&oacute;n nos complace haber contado con tu visita en nuestro Stand. En ese sentido, te hacemos saber que hemos registrado la misma, a fin de adherir a las condiciones exclusivas diseñadas para <b>Expoagro 2023 &quot;Bonificaci&oacute;n de Tasa para el financiamiento de Maquinaria Nacional&quot;.<sup>i</sup></b></p><p>En los pr&oacute;ximos d&iacute;as ser&aacute;s contactado por la Sucursal seleccionada, para avanzar con tu solicitud.</p><p>No obstante lo anterior, te dejamos los datos de la <a href=https://www.bna.com.ar/Institucional/Sucursales>Sucursal</a> por cualquier duda o consulta que necesites realizar.</p><p><b>EN BANCO NACI&Oacute;N CADA EMPRESA CUENTA, CADA ARGENTIN&#64; CUENTA</b></p><p><small><b>Condiciones:</b> Sujeto a proceso de vinculaci&oacute;n y/o an&aacute;lisis crediticio del Banco, y a la aprobaci&oacute;n y/o regulaciones que sobre la materia pudieran corresponder, emanada de organismos de contralor. La presente no es una oferta crediticia. Las condiciones de la presente pueden ser modificadas unilateralmente por el Banco en cualquier momento y sin previo aviso.</p><p> La Bonificaci&oacute;n tiene un monto m&aacute;ximo por MiPyME de &#36;40.000.000 (cuarenta millones de pesos). El plazo m&aacute;ximo de contabilizaci&oacute;n es hasta el 31/05/2023, debiendo la MiPyME mantener un Paquete de Servicios Empresas (Campo, Empresas o Pyme), realizar la compra del bien por BNA Conecta (www.bnaconecta.com.ar) y figurar en nuestros registros como visitante de nuestro Stand.</small></p>",
            "sql" : "SELECT Nombre, Apellido FROM FrmExpoAgro WHERE NroCuit = @NroCuit"
          }
    }
~~~

## 15. controls

## Generalidades

- El tag "name" hace referencia a la columna asociada al control en BD. Si vamos a guardar el nombre de una persona en la columna "nombreCompleto" de la tabla persona, el name del control debe ser "nombreCompleto"
- El tag "text" hace referencia al texto que va a mostrar el control
- Los tags "text" y "type" son obligatorios 
- El tag "name" es obligatorio a excepcion de cuando se define un control del tipo "title_control"
- El tag "attributes" es opcional

## Tipos de Controles Válidos


## text

### ***Descripción***

- Permite crear un campo de texto básico de una sola línea

### ***Atributos personalizados***

| Atributo | Descripción |
| ------------| ---------- |
| maxlength | Número máximo de caracteres que debe aceptar la entrada |


### ***Ejemplo de Uso***

~~~
{
    "name": "Nombre",
    "type": "text",
    "text": "Nombre",
    "attributes": {
        "maxlength": 100
    }
}  
~~~

## number

### ***Descripción***

- Permite al usuario ingresar un número
		
- Incluye validación incorporada para rechazar entradas no numéricas

### ***Atributos personalizados***

| Atributo | Descripción |
| ------------| ---------- |
| max |	El valor máximo a aceptar para esta entrada |		
| min	|El valor mínimo a aceptar para esta entrada |

### ***Ejemplo de Uso***

~~~
{
	"name": "dni",
    "type": "number",
    "text": "DNI",
    "attributes": {
      "min": 10000000,
      "max": 99999999
    }
}
~~~

## email

### ***Descripción***

- Permite al usuario ingresar una dirección de correo electronico

### ***Ejemplo de Uso***

~~~
{
    "name": "CorreoElectronico",
    "type": "email",
    "text": "Correo Electrónico Empresa"
}
~~~

## cuit

### ***Descripción***

- Permite al usuario ingresar un cuit
			
- Incluye validación incorporada para rechazar entradas no numéricas
			
- Incluye validación personalizada para corroborar que el cuit ingresado sea válido
			
- Limitando el rango por medio de los atributos min y max, permitimos al usuario ingresar un cuit asociado a una persona fisica o juridica

### ***Ejemplo de Uso***

~~~
{
    "name": "nroCuit",
    "type": "cuit",
    "text": "CUIT",
    "attributes": {
      "min": 30000000000,
      "max": 39999999999
    }
}
~~~

## tel

### ***Descripción***

- Permite al usuario ingresar un telefono 
			
- Incluye validación incorporada para rechazar entradas no numéricas
			
- Incluye validación personalizada para corrobar que el codigo de area ingresado sea válido

### ***Atributos personalizados***

| Atributo | Descripción |
| ------------| ---------- |
| max |	El valor máximo a aceptar para esta entrada |		
| min	|El valor mínimo a aceptar para esta entrada |

### ***Ejemplo de Uso***

~~~
{
  "name": "Telefono",
  "type": "tel",
  "text": "Teléfono",
  "attributes": {
      "min": 100000,
      "max": 99999999
  }
}
~~~

## decimal

### ***Descripción***

- Permite al usuario ingresar un numero decimal
			
- Incluye validación incorporada para rechazar entradas no numéricas

### ***Atributos personalizados***

| Atributo | Descripción |
| ------------| ---------- |
| max |	El valor máximo a aceptar para esta entrada |		
| min	|El valor mínimo a aceptar para esta entrada |

### ***Ejemplo de Uso***

~~~
{
  "name": "MontoSolicitado",
  "type": "decimal",
  "text": "Valor Referencia en Pesos",
  "attributes": {
    "min": 300000,
    "max": 10000000      
  }
}
~~~

## decimal_precalificado

### ***Descripción***

- Permite al usuario ingresar un numero decimal
		
- Incluye validación incorporada para rechazar entradas no numéricas
		
- El monto maximo que acepta esta entrada, se setea desde BD. Util para solicitudes con montos preaprobados

### ***Atributos personalizados***

| Atributo | Descripción |
| ------------| ---------- |
| max |	El valor máximo a aceptar para esta entrada |		
| min	|El valor mínimo a aceptar para esta entrada |

### ***Ejemplo de Uso***

~~~
"sql" : {
      "get" : "SELECT a.Apellido,a.Nombre,a.cuilt,a.documento, Monto AS montoPreacordado FROM AdhesionYBajaProducto_identificacion AS a INNER JOIN PrecalificadosGarrahan AS p ON a.cuilt = p.Cuil WHERE a.id = @id AND a.code = @code AND a.codigoProducto = 'Garrahan'"
}
~~~

~~~
{
  "name": "montoPreacordado",
  "type": "monto_precalificado"
},
{
  "name": "montoAcordado",
  "type": "decimal_precalificado",
  "text": "Monto solicitado",
  "attributes": {
    "min": 50000,
    "max": "montoPreacordado"      
  }
}
~~~

## select_string

### ***Descripción***

- Permite al usuario seleccionar una opcion desde un menú de opciones
		
- Las opciones disponibles desde el menu, se harcodean en el archivo

### ***Ejemplo de Uso***

~~~
{
  "name": "plazo",
  "type": "select_string",
  "text": "Plazo preferencia",
  "value" : [
    {"text" : "36 meses" , "value" : "36"},
    {"text" : "48 meses" , "value" : "48"},
    {"text" : "60 meses" , "value" : "60"},
    {"text" : "72 meses" , "value" : "72"}
  ]
}
~~~

## select_query

### ***Descripción***

- Permite al usuario seleccionar una opcion desde un menú de opciones

- Las opciones disponibles desde el menu, se traen de la BD

### ***Ejemplo de Uso***

~~~
{
  "name": "Fabricante",
  "type": "select_query",
  "text": "Fabricante",
  "value": {
    "query": "SELECT nombre as text,id as value FROM ExpoAgro_Fabricantes;"
  }
}
~~~

## sucursal

### ***Descripción***

- Permite al usuario seleccionar su sucursal de preferencia

### ***Ejemplo de Uso***

~~~
{
    "name": "sucursal",
    "type": "sucursal",
    "text": "Sucursal"
}
~~~

## checkbox s/ atributo href (default)

### ***Descripción***

- Permite al usuario seleccionar (o no) un valor booleano
		
- Si no se especifica el atributo "href", es OPCIONAL tildar el control

### ***Ejemplo de Uso***

~~~
{
  "name": "suscripcion",
  "type": "checkbox",
  "text": "Deseás recibir novedades sobre promociones y beneficios"
}
~~~

## checkbox c/ atributo href (Terminos y Condiciones)

### ***Descripción***

- Permite al usuario seleccionar un valor booleano
		
- Si se especifica el atributo "href" es OBLIGATORIO tildar el control para continuar

### ***Ejemplo de Uso***

~~~
{
  "name": "terminosCondiciones",
  "type": "checkbox",
  "text": "He leido y acepto los terminos y condiciones",
  "attributes": {
    "href": "https://www.bna.com.ar/Downloads/CondicionesDeLaBilleteraBNAmas.pdf"
  }
}
~~~

## title_control

### ***Descripción***

- Permite ingresar un subtitulo a mitad de un formulario para dividir la informacion

### ***Ejemplo de Uso***

~~~
{
    "type": "title_control",
    "text": "Contacto empresa"
}
~~~

