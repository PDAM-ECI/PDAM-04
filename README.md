# PDAM-04

En este laboratorio aprenderemos a usar el servicio de Firebase para almacenar información y
autenticar usuarios

## Instalación de ionic
Repita el paso de laboratorios anteriores para incializar la aplicación de ionic

```bash
ionic start pdam-4 sidemenu
```

Esto generará la aplicación base dentro de la carpeta pdam-4 con un menu lateral. confirme que la instalación 
haya sido exitosa corriendo la aplicación en modo web.

## Configuración de Firebase

Navegue a la pagina de Firebase de Google para crear una cuenta

Cree un nuevo proyecto en la consola de Firebase

## Configuración de la autenticación de usuario en Firebase

Habilite la autenticación con usuario y contraseña. Hay otro tipo de proveedores de autenticación.
Sin embargo, en este laboratorio usaremos la de usuario-contraseña

## Integración de firebase con ionic

En la sección de overview haga clic sobre el icono de "Añadir Firebase a su aplicación Web", copie
el script que se genera en este paso, incluyalo en el index.html dentro de la etiqueta <head>

Para integrar angular con Firebase usaremos esta libería, instale la libreria en el proyecto de ionic
 
```bash
bower install angularfire --save
```
 
inyecte el modulo de firebase en su aplicación.

```javascript
angular.module('starter', ['ionic', 'starter.controllers', 'firebase'])
```

Dentro de los controladores o servicios puede inyectar los siguientes servicios:
* $firebaseObject
* $firebaseArray
* $firebaseAuth

Cree un servicio que encargue del login y registro de los usuarios, este servicio debe contar con los siguientes metodos
* registerUser(username, pasword)
* login(username,password)

Para esto cree un nuevo archivo services.js dentro de la carpeta "js", creelos servicios dentro de un modulo llamado
starter.services. Puede guiarse del módulo de controladores y del siguiente ejemplo

```javascript
angular.module('starter.services', [])

    .service('UserService', function($firebaseAuth) {
        ...
    })
```

No olvide inyectar este nuevo modulo dentro de su aplicación.

### Registro

Para implementar el registro del usuario puede usar el metodo "$createUserWithEmailAndPassword" como en el siguiente ejemplo

```javascript

var auth = $firebaseAuth();
auth.$createUserWithEmailAndPassword(username, password).then(function () {
    ...
})

```

Recuerde que estos metodos devuelven objetos de tipo "Promise" ya que son llamados asíncronos.

Cree una nueva vista, ruta y controlador desde donde pueda registrar a un usuario usando email y contraseña, 
en el controlador inyecte el servicio de usuarios para poder usar la funcionalidad de registro creada en el punto anterior.
 
 

### Autenticación

En el servicio que se creó en el punto anterior implemente las funciones con ayuda de la libreria angularfire. Para la 
autenticación del usuario puede basarse en el siguiente ejemplo:

```javascript

var auth = $firebaseAuth();

auth.$signInWithEmailAndPassword(username, password).then(function () {
    ...
})
```

El objetivo de este servicio, es que pueda ser utilizado en otros controladores, por ejemplo en el controlador de la aplicación
"AppCtrl" que actualmente esta creado en el template. Despues de implementar la función de autenticación, inyecte el servicio
en este controlador y remplaze la implementación de la funcion "doLogin" para incluir la función del servicio de usuarios.

 
