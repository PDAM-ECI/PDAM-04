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

Para integrar angular con Firebase usaremos [esta](https://github.com/firebase/angularfire) libería, instale 
la libreria en el proyecto de ionic
 
```bash
bower install angularfire --save
```
 
inyecte el modulo de firebase en su aplicación e incluya el archivo en el index.html

```html
    ...
    <script src="lib/ionic/js/ionic.bundle.js"></script>
    
    <script src="lib/angularfire/dist/angularfire.min.js"></script>
    ...
```

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

No olvide inyectar este nuevo modulo dentro de su aplicación, ademas de incluir el archivo en el index.html.

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
 
El resultado final debe ser:

![alt text](http://i.giphy.com/26xBssIjJgsTcdqtq.gif)


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


Dentro del servicio de usuarios, implemente el siguiente metodo para obtener información sobre el usuario que se autenticó

```javascript
angular.module('starter.services', [])

    .service('UserService', function ($firebaseAuth) {
        var auth = $firebaseAuth();
        
        ...

        this.getUser = function () {
            return auth.$getAuth();
        };
    });
```

Este metodo puede ser usado para onbtener el ID del usuario, esta información se puede usar para relacionar otros datos usando
 el servicio de la base de datos en tiempo real de firebase.
 
## Firebase Storage: Real time Database

Usaremos este servicio para guardar información relacionada con el usuario, los datos en firebase se almacenan usando una 
estructura anidada de nombres, parecido al sistema de archivos con carpetas. Usando la libreria de firebase para angular
construya un servicio que se encargue de la interacción con la base de datos. Este servicio debe tener las siguientes funciones

* createPlaylist(userId, name, description)
* getPlaylist(userId)

Usando el siguiente ejemplo, cree, por cada usuario, una lista de playlist. La estructura sugerida para firebase es:

```bash
-playlist
--{user_id_1}
---{name:'nombre',description:'descripcion'}
---{name:'nombre 2',description:'descripcion'}
---{name:'nombre 3',description:'descripcion'}
---{name:'nombre 4',description:'descripcion'}
...

```

para insertar elementos en firebase guiese por el siguiente ejemplo

```javascript
firebaseRef.child('playlist').child(userId).push({
    name: name,
    description: description
}).then(function (res) {
    ....
});
```

para leer informacion en firebase guiese por el siguiente ejemplo

```javascript
firebase.database().ref().child('playlist').child(userId).once('value', function (snapshot) {
    snapshot.val();
});
```

Los controladores deben quedar al final de la siguiente manera

```javascript
    ...

    .controller('PlaylistCreateCtrl', function ($scope, $state, DataService, UserService) {
        $scope.data = {};

        var user = UserService.getUser();

        $scope.createPlaylist = function () {
            DataService.createPlaylist(user.uid, $scope.data.name, $scope.data.description).then(function (res) {
                console.log(res);

                $state.go('app.playlists');
            });
        };

        $scope.$on("$ionicView.enter", function (event, data) {
            $scope.data = {};
        });
    })

    .controller('PlaylistsCtrl', function ($scope, UserService, DataService) {
        $scope.playlists = [];

        var user = UserService.getUser();

        function loadPlaylist() {
            DataService.getPlaylist(user.uid).then(function (res) {
                console.log(res);
                $scope.playlists = res;
            });
        }

        $scope.$on("$ionicView.enter", function (event, data) {
            loadPlaylist();
        });
    })
    
    ...
```

Este debe ser el resultado final:
![alt text](http://i.giphy.com/26xByyFtftHmdWghy.gif)
