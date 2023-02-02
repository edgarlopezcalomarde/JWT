
# ¿Que es JWT?

Son siglas que hacen referencia **JSON WEB TOKEN**.

Este es un estandar abierto (RFC7519) autocontenido y compacto para transmitir informacion segura entre distintas partes utilizando objetos JSON.


Un objeto JSON seria algo asi:


``` JSON
{
    "personas":[
        {
            "id":1,
            "nombre":"Juan"
            "edad":20
        },
        {
            "id":2,
            "nombre":"Olga"
            "edad":20
        },
        {
            "id":3,
            "nombre":"Yaiza"
            "edad":20
        },
        {
            "id":4,
            "nombre":"Edgar"
            "edad":20
        }

    ]
}

```

# Caracterisitcas principales

La principal caracterisitca de JWT es que la informacion puede ser verificiada porque esta **firmada digitalmente**.

Para el que no lo sepa, una firma digital es un conjunto de datos asociados a un mensaje que permite asegurar la identidad del firmante y la integridad del mensaje. 

Para firmar este token se utiliza un secreto que solo conoce el cliente y el servidor o las las aplicaciones que se quieran conectar, que utilizan un algoritmo para encyptar ese secreto que puede ser una clave publica o privada.

*Conclusion utilizamos un secreto hasheado con sha256 para firmar el token y ya esta* 


**Compacto**: tiene un tamaño pequeño, esto le permite ser enviado atraves de la URL, como parametro POST o en una cabecera HTTP, como resultado de ser pequeño , es mas rapido de enviar.

**Autocontenido**: La informacion que contiene el JSON de JWT posee toda la informacion necesaria del usuario evitando llamadas inecesarias a BBDD.

**PD**: En nuestro caso somos bobitos y lo unico que guardamos en el token  es el subject que hace referencia a la id del usuario que ha creado el token y luego se realiza una llamada a la bbdd para verificar que existe el usuario propietario del token, no es que sea una llamada inecesaria porque si o si la necesitamos pero podriamos haber guardado otro tipo de informacion como el nombre pero a gustos colores crack.

# Usos comunes

**Autenticacion**: Este es el caso mas comun (aplicado a nuestro proyecto), donde un usuario utiliza el JWT en cada peticion que haga a la api (esto lo he puesto a la ligera no tiene porque ser en cada peticion se trata de mantener aislado rutas en el servidor y que no puedas acceder a estas a menos que estes logueado, en nuestro caso la ruta para acceder a los cosmeticos y a las categorias no necesita del JWT y para acceder a los carritos y a la informacion del usuario si).

**Intercambio de información**: Se puede usar para transmitir información entre distintos sistemas de forma segura. Porque JWT
puede ser firmado, se puede estar seguro que el que envía el JWT es quien dice ser. De modo que la firma es calculada usando el contenido del JWT lo que implica que podemos asegurar que el contenido no ha sido tocado en el proceso de transmisión


# De que se compone el JWT

Este se compone de 3 partes separadas por puntos.

Cabecera(Header)

Cuerpo(Payload)

Firma(Signature)


Insertar imagen de como se veria un JWT


### Cabecera
Esta a su vez se compone de:
Tipo de token
Algoritmo HASH que se esta usando

Insertar imagen de cabecera

### Cuerpo 

La informacion contenida en el cuerpo (Se les denomina **claims** puestos a ser tecnicos) es bastante util:

Registrados: Es un conjunto de informacion que viene predefinida el token.
 
- iss(emisor) : indica quien ha emitido el JWT.
- sub(objeto) : Identifica el princiapl objeto del JWT .
- aud(audiencia) : Identifica el principal destinatario.
- exp(Tiempo Expiracion) : Identifica cuando deja de ser valido el token.
- nbf(No depues) :Identifica la fecha/hora desde que el JWT.empieza a ser valido. 
- iat(Fecha de emison): Fecha en la que se emitio el JWT.
-jti(Id unica): Identificadr unico del jWT.

Publico: Este conjunto de informacion la definimos nostros como  personas que usamos el JWT, ej: name: "pepe".

Privado: Aquella informacion que no se encuentra en los conjuntos anteriores. Ej: Puede ser que una libreria genere informacion dentro del token.


### Firma

Pa que se entienda porque me canse de escribir seria algo asi:
    SHA256(base64(cabecera) + base64(cuerpo) + Secret Key)


LLegamos al final por fin y nos enctramos que el token seria algo asi(apartir de aqui me volvi perezoso pero lo esquematizo a modo de funcion como antes en la que doy a entender que que se codificar en el formato que tiene como nombre la funcion):


base64(header).base64(cuerpo).base64(firma)

teniendo en cuenta que la firma era algo asin :
    SHA256(base64(cabecera) + base64(cuerpo) + Secret Key)

Al final como resultado:

insertar foto aqui de JWT


## Funcionamiento


Como ejemplo yo expongo el que hemos implmentado en el proyecto,
que obviamente es el de la autenticacion.

En primer lugar el usuario se logea introduce nombre y contraseña se busca por nombre y si la contraseña HASHEADA coincide con la contraseña HASHEADA de la bbdd te devuelve el JWT, el cual se guarda localmente.

Cuando el usuario quieres acceder a una ruta o recurso protegido dentro de nuestra api, se envía el JWT para verificar que dicho usuario es quien dice ser y si esto es asi se le da permiso para acceder a la ruta o recurso.

Este token se envia atraves de la cabecera HTTP Authorization usando el esquema Bearer.

    Authorization:Bearer token

Este es un mecanismo de autenticacion sin  estado (stateless) ya que el estado del usuario nunca es almacenado en la memoria del servidor.













