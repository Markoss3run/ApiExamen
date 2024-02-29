# TUTORIAL API

### Definiciones

+ **API** -> (Que una aplicacion sea consumida por otra) Las API son mecanismos que permiten a dos componentes de software comunicarse entre sí mediante un conjunto de definiciones y protocolos.

+ **SOAP** -> Es un protocolo estándar que define cómo dos objetos en diferentes procesos pueden comunicarse por medio de intercambio de datos XML

+ **REST** -> Especificacion de como crear las API's

+ **Controladores** --> Parte encargada de coger los datos de la base de datos y entregarlos

+ **Reosurces** --> Hay un metodo (ToArray) donde personalizamos el API

+ **Request** --> Donde voy a validar los datos

+ **Middleware** -->Es una clase con un manejador cuyo contenido al ejecutarse entre la solicitud y la respuesta que validara algo

### Creamos el proyecto

Escribimos en la terminal

```bash
        laravel new nombre_Proyecto 
``` 

Le damos a todas las opciones predeterminadas y a la base de datos a MySQL.

Nos metemos dentro de la carpeta del proyecto que acabamos de crear

```bash
        cd nombre_Proyecto
```  


### 2º Una vez dentro  creamos el factory y el modelo

!!! "Nombre" es el nombre que le damos a nuestra api

Escribimos en la terminal lo siguiente:

```bash
        php artisan make:model Nombre --api -fm  
```


### 3º Realizamos la base de datos

Creamos los archivos .env (ya estará creado) y el docker-compose.yaml

#### .env

```php
        APP_NAME=Laravel
	APP_ENV=local
	APP_KEY=base64:2LsZA1WpEGaIoFovyisJ8NXwi+oFyqCmn9nRmLk/Sxg=
	APP_DEBUG=true
	APP_URL=http://localhost

	LOG_CHANNEL=stack
	LOG_DEPRECATIONS_CHANNEL=null
	LOG_LEVEL=debug

	DB_CONNECTION=mysql
	DB_HOST=127.0.0.1
	DB_PORT=23306
	DB_DATABASE=instituto (Nombre de la base de datos)
	DB_USERNAME=alumno
	DB_PASSWORD=alumno
	DB_PASSWORD_ROOT=root
	DB_PORT_PHPMYADMIN=8080

	BROADCAST_DRIVER=log
	CACHE_DRIVER=file
	FILESYSTEM_DISK=local
	QUEUE_CONNECTION=sync
	SESSION_DRIVER=file
	SESSION_LIFETIME=120

	MEMCACHED_HOST=127.0.0.1

	REDIS_HOST=127.0.0.1
	REDIS_PASSWORD=null
	REDIS_PORT=6379

	MAIL_MAILER=smtp
	MAIL_HOST=mailpit
	MAIL_PORT=1025
	MAIL_USERNAME=null
	MAIL_PASSWORD=null
	MAIL_ENCRYPTION=null
	MAIL_FROM_ADDRESS="hello@example.com"
	MAIL_FROM_NAME="${APP_NAME}"

	AWS_ACCESS_KEY_ID=
	AWS_SECRET_ACCESS_KEY=
	AWS_DEFAULT_REGION=us-east-1
	AWS_BUCKET=
	AWS_USE_PATH_STYLE_ENDPOINT=false

	PUSHER_APP_ID=
	PUSHER_APP_KEY=
	PUSHER_APP_SECRET=
	PUSHER_HOST=
	PUSHER_PORT=443
	PUSHER_SCHEME=https
	PUSHER_APP_CLUSTER=mt1

	VITE_APP_NAME="${APP_NAME}"
	VITE_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
	VITE_PUSHER_HOST="${PUSHER_HOST}"
	VITE_PUSHER_PORT="${PUSHER_PORT}"
	VITE_PUSHER_SCHEME="${PUSHER_SCHEME}"
	VITE_PUSHER_APP_CLUSTER="${PUSHER_APP_CLUSTER}"

	LANG_FAKE ="es_ES"

```

#### docker-compose.yaml

```php
        #Nombre de la version
version: "3.8"
services:
  mysql:
    # image: mysql <- Esta es otra opcion si no hacemos el build
    image: mysql

    # Para no perder los datos cuando destryamos el contenedor, se guardara en ese derectorio
    volumes:
      - ./datos:/var/lib/mysql
    ports:
      - ${DB_PORT}:3306
    environment:
      - MYSQL_USER=${DB_USERNAME}
      - MYSQL_PASSWORD=${DB_PASSWORD}
      - MYSQL_DATABASE=${DB_DATABASE}
      - MYSQL_ROOT_PASSWORD=${DB_PASSWORD_ROOT}

  phpmyadmin:
    image: phpmyadmin
    container_name: phpmyadmin  #Si no te pone por defecto el nombre_directorio-nombre_servicio
    ports:
      - ${DB_PORT_PHPMYADMIN}:80
    depends_on:
      - mysql
    environment:
      PMA_ARBITRARY: 1 #Para permitir acceder a phpmyadmin desde otra maquina
      PMA_HOST: mysql

```

### 4º Levantamos el docker

Para ello tendremos que parar primero los que tenemos en marcha y después eliminarlos.

```bash
        docker stop $(docker ps -a -q)  

        docker rm $(docker ps -a -q)
```

Una vez eliminados procedemos a levantarlo

```bash
        docker compose up -d  
```

Iniciamos el servidor

```bash
        php artisan serve
```


### 5º Poblamos la base de datos

Vamos a *database/factories/NombreFactory.php

```php
        //Este método normalmente se utiliza para definir las características o atributos de los datos que se generarán
        public function definition(): array
        {
                return [
                //Genera nombres aleatorios utilizando el generador de datos faker para nombres
                "nombre" =>fake()->name(),
                //Genera direcciones aleatorios utilizando el generador de datos faker para direcciones
                "direccion" =>fake()->address(),
                //Genera gmails aleatorios utilizando el generador de datos faker para gmails
                "email" =>fake()->email()
                //
                ];
        }
```

Vamos a *database/seeders/DatabaseSeeder.php

```php
        //Un seeder es utilizado para poblar la base de datos con datos de prueba o datos iniciales
        //Esta función indica que se utilizará el factory asociado al modelo Nombre para generar datos simulados y el create se utiliza para crear las instancias de los modelos utilizando el factory especificado. En este caso se crean 20 instancias del modelo
        Nombre::factory(20)->create();
```


Vamos a *config/app.php* para cambiar el idioma a la hora de crear los nombres (En este caso)

```php  
        //Se utiliza para establecer el idioma utilizado por el generador de datos ficticios (Faker). En este caso los pone en español
        'faker_locale' => 'es_ES',  
```


Vamos a la función up de *database/migrations/2024_02_20_092559_create_nombre_table.php*

```php
        //Se encarga de definir la estructura de la tabla nombres en la base de datos
        //Esto indica que se va a crear una nueva tabla en la base de datos con el nombre nombres. La función Schema::create() es utilizada para crear una nueva tabla, y recibe dos argumentos: el nombre de la tabla y una función de callback que define la estructura de la tabla.
        Schema::create('nombres', function (Blueprint $table) {
                //Esto agrega un campo autoincremental de tipo entero como clave primaria
                $table->id();
                //Esto agrega un campo de tipo cadena de texto llamado nombre. 
                $table->string("nombre");
                //Esto agrega un campo de tipo cadena de texto llamado direccion
                $table->string("direccion");
                //Esto agrega un campo de tipo cadena de texto llamado email
                $table->string("email");
                //Esto agrega automáticamente dos campos adicionales a la tabla llamados created_at y updated_at, que se utilizan para registrar la fecha y hora de creación y la fecha y hora de actualización de cada registro, respectivamente.
                $table->timestamps();
            });
```

Escribimos en la terminal el siguiente comando para poblar la base de datos con los requisitos que le hemos pedido.

```bash
        php artisan migrate --seed 
```     


### 6º Vamos a darle formato a los datos de la api

Creamos el NombreResource.php , NombreCollection.php y NombreFormRequest.php en la terminal

**NombreResource.php** --> Te permite definir cómo se presentarán los datos del modelo Nombre

**NombreCollection.php** --> Te permite definir cómo se presentarán colecciones de modelos Nombre

**NombreFormRequest.php** --> Te permite definir reglas de validación para los datos que esperas recibir en una solicitud

```bash 
        php artisan make:request NombreFormRequest
        php artisan make:resource NombreResource
        php artisan make:resource NombreCollection
```

Vamos a la función index y show de *app/Http/Controllers/NombreController.php*

```php
        //Esta función se encarga de manejar las solicitudes GET 
        public function index()
        {
            //Obtiene todos los registros de la tabla nombres de la base de datos 
            $nombres = Nombre::all();
            //Serializa los datos obtenidos del modelo en formato JSON antes de enviarlos como respuesta
            return response()->json($nombres);
        }

        //Esta función maneja las solicitudes GET para mostrar un nombre específico, identificado por su ID
        public function show(Nombre $nombre)
        {
             // Esto devuelve una respuesta que incluye un solo nombre en formato JSON
             return new NombreResource($nombre);
        } 
```


Vamos a la función toArray de *app/Http/Resources/NombreCollection.php*

```php
        //Se utiliza para convertir la colección de recursos en un array
        public function toArray(Request $request): array{
                return parent::toArray($request);
        }
        
        //Se utiliza para agregar datos adicionales a la respuesta JSON
        public function with(Request $request)
        {
                return[
                "jsonapi" => [
                        "version"=>"1.0"
                ]
                ];
        }
```   

Vamos a *app/Http/Resources/NombreResoruces.php* y añafimos estas funciones

```php
        //Devuelve un array que representa un recurso individual de nombre en una respuesta JSON
        public function toArray(Request $request): array{
        return[
            "id"=>$this->id,
            "type" => "Nombre",
            "attributes" => [
                "nombre"=>$this->nombre,
                "direccion"=>$this->direccion,
                "email"=>$this->email,
            ],
            "link"=>url('api/nombres'.$this->id)
        ];
    }
```

Ponemon la ruta de la api en routes/api.php

```php
        //Esta función define una serie de rutas RESTful para una entidad específica en tu aplicación
        Route::apiResource("nombres",\App\Http\Controllers\NombreController::class);
```

### 7º Para que te salten errores si no hay base de datos

Vamos a app/Exceptions/Handler.php y añadimos esto

**Handler.php** --> Esta clase es responsable de manejar todas las excepciones que ocurren durante la ejecución de la aplicación

```php
        use Illuminate\Database\QueryException;
        use PHPUnit\Event\Code\Throwable;

        //Es responsable de manejar cómo se representan las excepciones antes de ser enviadas al cliente
        public function render($request, Throwable $exception)
        {
        // Errores de base de datos)
                if ($exception instanceof QueryException) {
                return response()->json([
                        'errors' => [ 
                        [
                                'status' => '500',
                                'title' => 'Database Error',
                                'detail' => 'Error procesando la respuesta. Inténtelo más tarde.'
                        ]
                        ]
                ], 500);
                }
        // Delegar a la implementación predeterminada para otras excepciones no manejadas
                return parent::render($request, $exception);
        }
```

Para comprobarlo tiramos el docker

```bash
        docker compose down
```

### 8º Creamos un middleware

**Middleware** --> Un middleware en Laravel es una capa intermedia entre la solicitud HTTP y la lógica de la aplicación

Escribimos en la terminal

```bash 
        php artisan make:middleware HandleMiddleware
```

Nos vamos a la carpeta app/Http/Middleware/HandleMiddleware.php y en la función handle ponemos esto

```php  
        //Verifica si las solicitudes entrantes especifican que esperan una respuesta en formato JSON API.
        public function handle(Request $request, Closure $next): Response
        {
                if ($request->header('accept') != 'application/vnd.api+json') {
                return response()->json([
                        "errors"=>[
                        "status"=>406,
                        "title"=>"Not Accetable",
                        "deatails"=>"Content File not specifed"
                        ]
                ],406);
                }
                return $next($request);
        }
```

Nos vamos a app/Http/Kernel.php para especificar que vas a usar tu middleware, lo ponemos en la sección api de middlewareGroups.

```php
        //Se utiliza para registrar el middleware HandleMiddleware en el kernel de la aplicación Laravel, lo que significa que este middleware será ejecutado antes de que las solicitudes API pasen al controlador correspondiente.
        HandleMiddleware::class
```

En postman creamos un header key "Accept" y value "application/vnd.api+json"


### 9º Cogemos excepciones de nombres que no encontramos

Nos vamos a app/Http/Controllers/NombreController.php y sustituimos el método show por

```php
        //Maneja solicitudes para mostrar un nombre específico. Si el nombre no se encuentra en la base de datos, devuelve un error 404, sino devielve el nombre con el formato específico
        public function show(int $id)
        {
                $nombre = Nombre::find($id);
                if(!$nombre)
                        return response()->json([
                                "errors"=>[
                                "status"=>404,
                                "title"=>"Resource not found",
                                "details"=>"$id Nombre not found"
                                ]
                        ],404
                );
                return new NombreResource($nombre);
        }
```

Nos vamos a app/Http/Controllers/NombreController.php y sustituimos el método store por:

```php
        //Se encarga de recibir los datos de entrada de la solicitud, que están estructurados según la especificación JSON API, y los asigna a la variable $datos
        public function store(NombreFormRequest $request)
        {
                $datos = $request->input("data.attributes");
        }
```     

Le damos permisos en app/Http/Requests/AlumnoRequest.php y sustituimos por esto:

```php
        //Se utiliza para autorizar las solicitudes relacionadas con los datos de los alumnos.
        public function authorize(): bool
        {
                return true;
        }

        //Se utiliza para validar las solicitudes relacionadas con los datos de los alumnos.
        public function rules(): array
        {
                return [
                        "data.attributes.nombre"=>"required|min:5",
                        "data.attributes.direccion"=>"required",
                        "data.attributes.email"=>"required|email|unique:alumnos"
                ];
        }
```

### 10º Validamo los datos inválidos de JSON

Vamos a app/Exceptions/Handler.php

```php

        use Illuminate\Http\JsonResponse;
        use Illuminate\Validation\ValidationException;

        //Personaliza la respuesta JSON devuelta cuando se produce una excepción de validación durante el procesamiento de una solicitud, proporcionando detalles específicos sobre los errores de validación 
        protected function invalidJson($request, ValidationException  $exception):JsonResponse
        {
                //Sirve para devolver un error por cada uno de los no requitsitos que cumplan cada dato de mi formulario
                return response()->json([
                'errors' => collect($exception->errors())->map(function ($message, $field) use
                ($exception) {
                        return [
                        'status' => '422',
                        'title' => 'Validation Error',
                        'details' => $message[0],
                        'source' => [
                                'pointer' => '/data/attributes/' . $field
                        ]
                        ];
                })->values()
                ], $exception->status);
        }
```

Añadimo al método render de app/Exceptions/Handler.php para llamar al método invalidJson

```php
        //Garantiza que cualquier excepción de validación se maneje correctamente llamando al método invalidJson
        if ($exception instanceof ValidationException) {
            return $this->invalidJson($request, $exception);
        }
```

### 11º Creamos artículos

Añadimos en  en app/http/Models/Nombre.php

```php
        //Propiedad en los modelos de Laravel que te permite especificar qué atributos pueden ser asignados masivamente, proporcionando una capa adicional de seguridad y control sobre tus datos de base de datos.
        protected $fillable=["nombre","direccion","email"];
```     

Modificamos el método store de app/http/Controllers/NombreController.php

```php
        //Toma los datos de entrada de la solicitud, crea un nuevo alumno utilizando esos datos y lo guarda en la base de datos. Luego, devuelve una respuesta JSON que representa el alumno recién creado
        public function store(AlumnoFormRequest $request)
        {
                $datos = $request->input("data.attributes");
                $alumno = new Alumno($datos);
                $alumno->save();
                return new AlumnoResource($alumno);
        }
```

Modificamos el método rules de app/http/Request/NombreFormRequest.php

```php
        //Estas reglas de validación aseguran que los datos enviados en la solicitud cumplan con ciertos criterios antes de ser aceptados y procesados
        public function rules(): array
        {
                return [
                "data.attributes.nombre"=>"required|min:5",
                "data.attributes.direccion"=>"required",
                "data.attributes.email"=>"required|email|unique:alumnos,email"
                ];
        }
```     

Si el docker no está levantado lo levantamos en la terminal

```bash
        docker compose up -d
```     

Para comprobarlo escribimos en el body del postman en método post

```bash
        {"data": {
                "type": "Alumnos",
                "attributes": {
                        "nombre": "Marcos Celimendiz",
                        "direccion": "Mi casa",
                        "email": "nombre@a.com"
                        }
                }
        }
```

### 12º Borramos artículo

Modificamos el método destroy de app/http/Controllers/NombreController.php

```php
        //Elimina un alumno de la base de datos y devuelve una respuesta JSON indicando el resultado de la operación
        public function destroy(int $id)
        {
                $alumno = Alumno::find($id);
                if (!$alumno) {
                return response()->json([
                        'errors' => [
                        [
                                'status' => '404',
                                'title' => 'Resource Not Found',
                                'detail' => 'The requested resource does not exist or could not be found.'
                        ]
                        ]
                ], 404);
                }
                $alumno->delete();
                return response()->json(null,204);
                //No devuele econtenido
                return response()->noContent();
        }
```

Para comprobarlo en postman ponemos el método DELETE y escribimos la url de un nombre

```bash
        http://127.0.0.1:8000/api/nombre/id
```

### 13º Actualizamos los datos

Modificamos el método update de app/http/Controllers/NombreController.php

```php
        public function update(Request $request, int $id)
    {
        $alumno = Alumno::find($id);
        if (!$alumno) {
            return response()->json([
                'errors' => [
                    [
                        'status' => '404',
                        'title' => 'Resource Not Found',
                        'detail' => 'The requested resource does not exist or could not be found.'
                    ]
                ]
            ], 404);
        }

        $verbo = $request->method();
        //En función del verbo creo unas reglas de
        // validación u otras
        if ($verbo == "PUT") { //Valido por PUT
            $rules = [
                "data.attributes.nombre" => ["required", "min:5"],
                "data.attributes.direccion" => "required",
                "data.attributes.email" => ["required", "email", Rule::unique("alumnos", "email")->ignore($alumno)]
            ];

        } else { //Valido por PATCH
            if ($request->has("data.attributes.nombre"))
                $rules["data.attributes.nombre"]= ["required", "min:5"];
            if ($request->has("data.attributes.direccion"))
                $rules["data.attributes.direccion"]= ["required"];
            if ($request->has("data.attributes.email"))
                $rules["data.attributes.email"]= ["required", "email", Rule::unique("alumnos", "email")->ignore($alumno)];
        }

        $datos_validados = $request->validate($rules);
        //dump($datos_validados);

        foreach ($datos_validados['data']['attributes'] as $campo=>$valor)
            $datos[$campo] = $valor;

        $alumno->update($request->input("data.attributes"));

        return new AlumnoResource($alumno);
    }
```

Para comprobarlo escribimos en el postman en método PATCH y PUT en body y en la url

**Método PATCH**

No haace falta poner todos los datos

```bash
        http://127.0.0.1:8000/api/nombre/id

        {
        "data": {
                "type": "Alumnos",
                "attributes": {
                        "nombre": "Hola"
                }
        }
        }
```

**Método PUT**

Hace falta poner todos los datos

```bash
        http://127.0.0.1:8000/api/nombre/id

        {
        "data": {
                "type": "Alumnos",
                "attributes": {
                        "nombre": "Marcos Celimendiz",
                        "direccion": "Mi casa",
                        "email": "nombree@a.com"
                }
        }
        }
```

### Comando para ver si esta todo bien configurado

Para saber el método del controlador que usa cada método del postman

´´´bash
php artisan route:list --path='api/alumnos'
´´´
