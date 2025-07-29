# GraphQL Introspection, Mutations e IDORs

Tags: #GrapQL #IDORs #Mutations #OWASP #Explotacion 

**GraphQL** es un lenguaje de consulta para **APIs** (**Application Programming Interfaces**) que se ha vuelto cada vez más popular en los últimos años. A diferencia de las APIs tradicionales que tienen endpoints fijos, GraphQL permite a los clientes solicitar sólo la información que necesitan y obtener una respuesta personalizada en función de sus necesidades.

**Introspection** en GraphQL es un mecanismo que permite a los clientes **obtener información** sobre el esquema GraphQL de una API. Esto significa que los clientes pueden explorar y descubrir los tipos de datos, los campos y las relaciones que existen en la API, lo que puede ser muy útil para los desarrolladores que necesitan construir clientes GraphQL. Sin embargo, la introspección también puede ser utilizada por los atacantes para obtener información sensible sobre la estructura y los datos que existen en la API, lo que puede ser utilizado para llevar a cabo ataques más sofisticados.

Por otro lado, las **Mutations** en GraphQL son operaciones que permiten a los clientes **modificar los datos** en la API. A diferencia de las consultas, que sólo permiten la lectura de datos, las mutaciones permiten a los clientes agregar, actualizar o eliminar datos. Esto significa que las mutaciones tienen el potencial de ser utilizadas para realizar cambios importantes en la base de datos subyacente de la API. Si no se protegen adecuadamente, las mutaciones pueden ser explotadas por los atacantes para realizar cambios malintencionados en la API, como la eliminación de datos importantes o la creación de nuevos registros.

En el contexto de GraphQL, los **IDORs** pueden ocurrir cuando un atacante es capaz de adivinar o enumerar identificadores (**IDs**) de objetos dentro de la API, y puede utilizar esos IDs para acceder a objetos a los que no debería tener acceso. Esto puede ocurrir porque los desarrolladores de la API pueden no haber implementado adecuadamente los mecanismos de autenticación y autorización en su API.

Por ejemplo, supongamos que una API GraphQL permite a los usuarios acceder a información sobre sus propios pedidos utilizando el ID de pedido. Si el desarrollador de la API no ha implementado la autorización adecuada, un atacante podría utilizar la introspección de GraphQL para descubrir todos los IDs de pedido existentes en la API, incluyendo los pedidos de otros usuarios. El atacante podría luego utilizar estos IDs para acceder a los pedidos de otros usuarios sin la debida autorización, lo que constituye un IDOR.

Los atacantes también pueden utilizar las Mutaciones de GraphQL para llevar a cabo ataques IDOR. Por ejemplo, supongamos que una API GraphQL permite a los usuarios actualizar la información de sus propios pedidos. Si el desarrollador de la API no ha implementado adecuadamente la autorización, un atacante podría utilizar una mutación para actualizar los datos de los pedidos de otros usuarios, utilizando los IDs de pedido que ha descubierto mediante la introspección.

En resumen, GraphQL es un lenguaje de consulta para APIs que permite a los clientes solicitar sólo la información que necesitan. Es importante que los desarrolladores de APIs protejan adecuadamente la introspección y las mutaciones para evitar ataques maliciosos por parte de los atacantes.

A continuación, se os proporciona el enlace directo a los distintos proyectos de Github los cuales empleamos en esta clase para enumerar y abusar del GraphQL:

- **GraphQL Introspection**: [https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Graphql-Introspection](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Graphql-Introspection)
- **GraphQL Mutations**: [https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Graphql-Mutations](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Graphql-Mutations)
- **GraphQL Injection**: [https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Graphql-Injection](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Graphql-Injection)
- **GraphQL IDOR**: [https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Graphql-IDOR](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Graphql-IDOR)


## GraphQL, Mutations, IDORs

* Lab1: GraphQL IDOR

En este lab 

![](Pasted%20image%2020230528182541.png)


Aquí podemos colocar el usuario y su passwd.
![](Pasted%20image%2020230528182726.png)


![](Pasted%20image%2020230528182654.png)

Haremos un Fuzzing para el descubrimiento de rutas con Gobuster.

```bash 
❯ gobuster dir -u http://localhost:5000/ -w /usr/share/seclists/Discovery/Web-Content/dirrectory-list-2.3-medium.txt -t 20 
```

Encontramos un 'Settings'
![](Pasted%20image%2020230528183055.png)

Al momento de interceptar esta petición con BurpSuite obtenemos lo siguiente.

![](Pasted%20image%2020230528183146.png)

Le damos en Forward y nos manda a otra solicitud diferente a graphql y tenemos una query. 
![](Pasted%20image%2020230528183213.png)

Al momento de darle Send obtenemos los campos del usuario. 

![](Pasted%20image%2020230528183521.png)

Para acontecer el IDOR podemos ir modificando el campo ID e ir enumerando a los diferentes usuarios. 

![](Pasted%20image%2020230528183703.png)



* Lab2: GraphQL Introspection

Para este lab nos podemos apoyar de la siguiente pagina. 
* [Graphql-Attack](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/graphql)

Tenemos una interfaz similar a la anterior y con este tipo de vulnerabilidad, solo podemos enumerar la información. 
![](Pasted%20image%2020230528184027.png)

Pero al momento de interceptarla con BurpSuite obtenemos lo siguiente. 

![](Pasted%20image%2020230528184140.png)

Le damos en Fordward y obtendremos la siguiente petición. 

![](Pasted%20image%2020230528184221.png)

Podemos usar la siguiente query en la URL de pagina web y así enumerar todos los tipos de campos. 

```bash 
query={__schema{types{name,fields{name}}}}
```

![](Pasted%20image%2020230528184553.png)

![](Pasted%20image%2020230528184616.png)

Ahora podemos ver en la derecha del graphql todos los tipos de campos. 


Ahora podemos enumerar todas las bases de datos. 
```bash 
/?query=fragment%20FullType%20on%20Type%20{+%20%20kind+%20%20name+%20%20description+%20%20fields%20{+%20%20%20%20name+%20%20%20%20description+%20%20%20%20args%20{+%20%20%20%20%20%20...InputValue+%20%20%20%20}+%20%20%20%20type%20{+%20%20%20%20%20%20...TypeRef+%20%20%20%20}+%20%20}+%20%20inputFields%20{+%20%20%20%20...InputValue+%20%20}+%20%20interfaces%20{+%20%20%20%20...TypeRef+%20%20}+%20%20enumValues%20{+%20%20%20%20name+%20%20%20%20description+%20%20}+%20%20possibleTypes%20{+%20%20%20%20...TypeRef+%20%20}+}++fragment%20InputValue%20on%20InputValue%20{+%20%20name+%20%20description+%20%20type%20{+%20%20%20%20...TypeRef+%20%20}+%20%20defaultValue+}++fragment%20TypeRef%20on%20Type%20{+%20%20kind+%20%20name+%20%20ofType%20{+%20%20%20%20kind+%20%20%20%20name+%20%20%20%20ofType%20{+%20%20%20%20%20%20kind+%20%20%20%20%20%20name+%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20%20%20%20%20%20%20}+%20%20%20%20%20%20%20%20%20%20%20%20}+%20%20%20%20%20%20%20%20%20%20}+%20%20%20%20%20%20%20%20}+%20%20%20%20%20%20}+%20%20%20%20}+%20%20}+}++query%20IntrospectionQuery%20{+%20%20schema%20{+%20%20%20%20queryType%20{+%20%20%20%20%20%20name+%20%20%20%20}+%20%20%20%20mutationType%20{+%20%20%20%20%20%20name+%20%20%20%20}+%20%20%20%20types%20{+%20%20%20%20%20%20...FullType+%20%20%20%20}+%20%20%20%20directives%20{+%20%20%20%20%20%20name+%20%20%20%20%20%20description+%20%20%20%20%20%20locations+%20%20%20%20%20%20args%20{+%20%20%20%20%20%20%20%20...InputValue+%20%20%20%20%20%20}+%20%20%20%20}+%20%20}+}
```

Debemos de corregir los errores que nos salen y así obtendríamos el siguiente output.
![](Pasted%20image%2020230528185120.png)


Para poder ver mas bonito ese output, podemos usar la siguiente pagina en modo Demo. 

* [Graphql-Voyager](https://graphql-kit.com/graphql-voyager/)

![](Pasted%20image%2020230528185621.png)

Copiamos el output anterior y lo pegamos ahí para poderlo ver mejor. 

![](Pasted%20image%2020230528185712.png)

Podemos ir modificando los campos en el BurpSuite mirando el grafico y así ir viendo el contenido mas fácilmente.
![](Pasted%20image%2020230528185745.png)



* Lab3: GraphQL Mutations

![](Pasted%20image%2020230528190037.png)

Podemos crear un post. 
Al momento de interceptarlo observamos los siguientes campos, los cuales podemos modificar. 

![](Pasted%20image%2020230528190237.png)

![](Pasted%20image%2020230528190340.png)

![](Pasted%20image%2020230528190354.png)