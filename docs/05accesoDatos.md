<style>
    img { margin: 20px 0; border-radius: 8px; }

    .alert { color: #BD1550; }
    .warning { color: #E97F02; }
    .success { color: #8A9B0F; }

    .center { text-align: center; }
    .right { text-align: right; }

    .img-small { max-width: 200px; margin: auto; }
    .img-medium { max-width: 400px; margin: auto; }
    .img-large { max-width: 800px; margin: auto; }

    .leyenda {
        font-size: small;
        margin: 10px 0;
    }
</style>

# Accès a dades
<!-- 
> Duració estimada: 14 hores
 -->
En aquesta unitat aprendrem a accedir a dades que es troben en un servidor; recuperant, editant i creant aquestes dades a través d'una base de dades.

A través de les diferents capes o nivells, de les quals 2 d'elles ja coneixem (*Nginx*, *PHP*) i *MySQL* la que estudiarem en aquest tema.

<div class="center img-large">
    <img src="imagenes/06/06-bbdd-arquitectura-3-niveles.png">
</div>

## Instal·lació
A través de **XAMPP** és molt senzill, simplement ens descarregaríem el programa i l'activaríem. Per a descarregar XAMPP [prem ací](https://www.apachefriends.org/es/download.html).

Amb ***Docker*** utilitzarem un altre  repositori que inclou el mysql i el phpMyAdmin i llancem
``` bash
docker-compose up -d
```

Si tot ha eixit bé i el contenidor està en marxa, podrem visitar la pàgina de phpMyAdmin de la següent manera
``` html
http://localhost:8000
```

<div class="center img-medium">
    <img src="imagenes/06/06-bbdd-phpMyAdmin-login.png">
</div>

Per a accedir hem d'utilitzar les següents credencials que venen configurades en el arxiu `docker-compose.yml`

```
usuario: root
contraseña: 1234
```

## Estructura d'una base de dades

Sabem que una base de dades té molts camps amb els seus noms i valors, però a més sabem que la base de dades ha de tindre un nom. per tant tindríem la següent estructura per a una base de dades:
    
    NombreBaseDeDatos
        |__Tabla-#1
        |       |__DatosTabla-#1
        |
        |__Tabla-#2
        |       |__DatosTabla-#2
        |
        |__Tabla-#3
        |       |__DatosTabla-#3
        [...]


Vegem-ho en un exemple real

    Ryanair
        |__pasajero
        |    |__id[*]
        |    |__nombre
        |    |__apellidos
        |    |__edad
        |    |__id_vuelo[^]
        |
        |__vuelo
        |    |__id[*]
        |    |__n_plazas
        |    |__disponible
        |    |__id_pais[^]
        |
        |__pais
             |__id[*]
             |__nombre

<div class="leyenda">
    [*] Clau primària [^] Clave Forània
</div>

<div class="center img-large">
    <img src="imagenes/06/06-bbdd-estructura.png">
</div>

## CholloSevero

Al llarg d'aquesta unitat treballarem amb una base de dades que anirem confeccionant conforme avancem, on emmagatzemarem la informació relacionada amb ofertes que publiquen els usuaris i els llistarem en funció de diversos filtres; nous, més votats, més vistos, més comentats entre altres, al més pur estil **[*Chollometro](https://www.chollometro.com)**.

<div class="center img-large">
    <img src="imagenes/06/06-chollometro.gif">
</div>

## SQL

Aquest llenguatge de consulta estructurada (*Structured Query Language*) és el que utilitzarem per a realitzar les consultes a les nostres bases de dades per a mostrar el contingut en les diferents interfícies web que creem al llarg de la unitat. Si vols saber més detalls visita [Wiki SQL](https://es.wikipedia.org/wiki/sql)

Exemple d'una sentència SQL on seleccionem totes les files i columnes de la nostra taula anomenada **'pais'**
``` sql
SELECT * FROM pais
```

Estas sentencias pueden invocarse desde la consola de comandos mediante el intérprete *mysql* (previamente instalado en el sistema) o a través de la herramienta phpMyAdmin.

Las sentencias SQL también las podemos usar dentro de nuestro código php, de tal manera que cuando se cargue nuestra interfaz web, lance una sentecia SQL para mostrar los datos que queramos.

``` php
<?php
    // Llistat de clients, adreçats per DNI de manera ASCendent
    $clientesOrdenadosPorDNI = "SELECT * FROM `pasajero` ORDER BY `dni`" ASC;
?>
```

## phpMyAdmin

<div class="center img-medium">
    <img src="imagenes/06/06-bbdd-phpMyAdmin-logo.png">
</div>

Aquest programari funciona sota Ngingx i PHP i és més que res una interfície web per a gestionar les bases de dades que tinguem disponibles en el nostre servidor local. Molts **hostings* ofereixen aquesta eina per defecte per a poder gestionar les BBDD que tinguem configurades sota el nostre compte.

### Creant una base de dades dins de phpMyAdmin

<div class="center img-large">
    <img src="imagenes/06/06-bbdd-phpMyAdmin.gif">
</div>

1.  Per a crear una nova base de dades hem d'entrar en *phpMyAdmin* com a *usuari root* i punxar en l'opció <span class="warning">*Nova*</span> del menú de l'esquerra.

2. En la nova finestra de creació posarem un **nom** a nostra *bbdd*.

3. També establirem el **cotejamiento** <span class="warning">*utf8m4_unicode_ci*</span> perquè nostra *bbdd* suporte tot tipus de caràcters (com els asiàtics) i fins i tot *emojis* ;)

4. Li donem al botó de **Crear** per a crear la *bbdd* i començar a escriure les diferents taules que anem a introduir en ella.

El sistema generarà el codi SQL per a crear tot el que li hem posat i crearà la base de dades amb les taules que li hàgem ficat.
``` sql
CREATE TABLE `persona`. ( `id` INT NOT NULL AUTO_INCREMENT , `nombre` TINYTEXT NOT NULL , `apellidos` TEXT NOT NULL , `telefono` TINYTEXT NOT NULL , PRIMARY KEY (`id`)) ENGINE = InnoDB;
```

### Opcions en phpMyAdmin

Quan seleccionem una base de dades de la llista, el sistema ens mostra diverses pestanyes amb les quals interactuar amb la base de dades en qüestió:

- `Estructura`: Podem veure les diferents taules que consoliden la nostra base de dades

- `SQL`: Per si volem injectar codi SQL perquè el sistema l'interprete

- `Buscar`: Serveix per a buscar per termes, en la nostra base de dades, aplicant diferents filtres de cerca

- `Generar consulta`: semblança a SQL però d'una manera més gràfica, sense haver de saber res del llenguatge

- `Exportar i importar`: Com el seu nom indica, per a fer qualsevol de les 2 operacions sobre la base de dades

- `Operacions`: Diferents opcions avançades per a realitzar en la nostra base de dades, de la qual destacarem l'opció *Cotejamiento* on podrem canviar el *cotejamiento* de la nostra taula però <span class="alert">*ULL AMB ACÔ* perquè podem eliminar dades sense voler, ja que en canviar el *cotejamiento* podem suprimir caràcters no suportats pel nou *cotejamiento*</span>

No aprofundirem en la resta d'opcions però, en la pestanya **Més** existeix l'opció **Dissenyador** per a poder editar les relacions entre taules d'una manera gràfica (punxant i arrossegant) que veurem més endavant.

## PHP Data Objects :: PDO

*PHP Data Objects* (o *PDO*) és un *driver* de *PHP* que s'utilitza per a treballar sota una interfície d'objectes amb la base de dades. Hui dia és el que més s'utilitza per a manejar informació des d'una base de dades, ja siga relacional o no relacional.

Per establir la connexió en la bbdd utilitzarem:

``` php
<?php
    $conexion = new PDO('mysql:host=localhost; dbname=dwes', 'dwes', 'abc123');
```

A més, amb *PDO* podem usar les excepcions amb *try catch* per a gestionar els errors que es produïsquen en la nostra aplicació, per a això, com féiem abans, hem d'encapsular el codi entre blocs *try / catch*.
``` php
<?php

    $dsn = 'mysql:dbname=prueba;host=127.0.0.1';
    $usuario = 'usuario';
    $contraseña = 'contraseña';

    try {
        $mbd = new PDO($dsn, $usuario, $contraseña);
        $mbd->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
    } catch (PDOException $e) {
        echo 'Falló la conexión: ' . $e->getMessage();
    }
```
En primer lloc, creem la connexió amb la base de dades a través del constructor *PDO* passant-li la informació de la base de dades.

En segon lloc, establim els paràmetres per a manejar les exempcions, en aquest cas hem utilitzat:

- `*PDO::ATTR_ERRMODE*` indicant-li a PHP que volem un reporte d'errors.
- `*PDO::ERRMODE_EXCEPTION*` amb aquest atribut obliguem al fet que llance exempcions, a més de ser l'opció més humana i llegible que hi ha a l'hora de controlar errors.

Qualsevol error que es llance a través de *PDO^, el sistema llançarà una <span class="alert">**PDOException**</span>.

### Fitxer de configuració de la BD

De la mateixa manera que podem tenir el nostre arxiu de funcions `funciones.php` i alberguem totes les funcions que s'usen de manera global en l'aplicació, podem establir un arxiu de constants on definim els paràmetres de connexió amb la base de dades.
```php
<?php

    //  ▒▒▒▒▒▒▒▒ conexion.php ▒▒▒▒▒▒▒▒

    constDSN = "mysql:host=localhost;dbname=dwes";
    constUSUARIO = "dwes";
    constPASSWORD = "abc123";

    /*  ▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒

        ▒▒▒▒▒▒▒▒ NO SUBAS ESTE ARCHIVO A git ▒▒▒▒▒

        ▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒ */

```

Aquest arxiu conté informació <span class="alert">**molt sensible**</span> així que no és recomanable que puges aquest arxiu a *git.

### Sentències preparades

Es tracta de sentències que s'estableixen com si foren plantilles de la SQL que llançarem, acceptant paràmetres que són establits a posteriori de la declaració de la sentència preparada.

Les sentències preparades eviten la *injecció* de SQL (SQL Injection) i milloren el rendiment de nostres *aplicacions* o pàgines web.

``` php
<?php
    $sql = "INSERT INTO Clientes VALUES (?, ?, ?, ?)";
```

Cada interrogant és un paràmetre que establirem després, unes quantes línies més a baix.

Una vegada tenim la plantilla de la nostra consulta, hem de seguir amb la preparació juntament amb 3 mètodes més de *PHP* per a la seua completa execució:

- `prepare:` prepara la *sentencia* abans de ser executada.
- `bind`: el tipus d'unió (*bind*) de dada que pot ser mitjançant ' ? ' o ' :parametre '
- `execute` s'executa la consulta unint la plantilla amb les *variables* o paràmetres que hem establit.

### Exemple paràmetros

```php
<?php
    //  ▒▒▒▒▒▒▒▒ Borrando con parámetros ▒▒▒▒▒▒▒▒

    include "config/database.inc.php";

    $conexion = null;

    try { 
        $cantidad = $_GET["cantidad"];

        $conexion = new PDO(DSN, USUARIO, PASSWORD);
        $conexion -> setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

        $sql = "DELETE FROM stock WHERE unidades = ?";
        $sentencia = $conexion -> prepare($sql);

        $isOk = $sentencia -> execute([$cantidad]);
        $cantidadAfectada = $sentencia -> rowCount();

        echo $cantidadAfectada;
    } catch (PDOException $e) {
        echo $e -> getMessage();
    }

    $conexion = null
```

### Exemple bindParam

Molt semblant a utilitzar paràmetres però aquesta vegada la variable està dins de la sentència SQL, en aquest cas l'hem anomenada `:cant`

```php
<?php
    include "config/database.inc.php";

    $conexion=null;

    try {
        $cantidad = $_GET["cantidad"] ?? 0;

        $conexion = new PDO(DSN, USUARIO, PASSWORD);
        $conexion -> setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

        $sql = "DELETE FROM stock WHERE unidades = :cant";

        $sentencia = $conexion -> prepare($sql);
        $sentencia -> bindParam(":cant", $cantidad);
        
        $isOk = $sentencia -> execute();
        
        $cantidadAfectada = $sentencia -> rowCount();
        
        echo $cantidadAfectada;
    } catch (PDOException $e) {
        echo $e -> getMessage();
    }

    $conexion = null;
```

### bindParam VS bindValue

Utilitzarem `bindValue()` quan hàgem d'inserir dades només una vegada, en canvi, haurem d'usar `bindParam()` quan hàgem de passar dades múltiples, com per exemple, un *array*.

```php
<?php
    // se asignan nombre a los parámetros
    $sql = "DELETE FROM stock WHERE unidades = :cant";
    $sentencia = $conexion -> prepare($sql);

    // bindParam enlaza por referencia
    $cantidad = 0;

    $sentencia -> bindParam(":cant", $cantidad);
    $cantidad = 1;

    // se eliminan con cant = 1
    $isOk = $sentencia -> execute();

    // bindValue enlaza por valor
    $cantidad = 0;

    $sentencia -> bindValue(":cant", $cantidad);
    $cantidad = 1;

    // se eliminan con cant = 0
    $isOk = $sentencia->execute();
```

Per a més informació i ús de les variables *PDO* [consulta el manual de PHP](https://www.php.net/manual/es/pdo.constants.php).

### Inserint registres

A l'hora d'inserir registres en una base de dades, hem de tindre en compte que en la taula pot haver-hi valors autoincrementats. Per a salvaguardar açò, el que hem de fer és deixar aqueix camp autoincrementat buit, però a l'hora de fer la connexió, hem de recuperar-ho amb el mètode `lastInsertId()`.

``` php
<?php
    $nombre = $_GET["nombre"] ?? "SUCURSAL X";
    $telefono = $_GET["telefono"] ?? "636123456";

    $sql="INSERT INTO tienda(nombre, tlf) VALUES (:nombre, :telefono)";

    $sentencia = $conexion -> prepare($sql);
    $sentencia -> bindParam(":nombre", $nombre);
    $sentencia -> bindParam(":telefono", $telefono);

    $isOk = $sentencia -> execute();
    $idGenerado = $conexion -> lastInsertId();

    echo $idGenerado;
```

### Consultant registres

A l'hora de recuperar els resultats d'una consulta, bastarà amb invocar al mètode `PDOStatement::fetch` per a llistar les files generades per la consulta.

Però hem de triar el tipus de dada que volem rebre entre els 3 que hi ha disponibles:

- `PDO::FETCH_ASSOC:` array indexat que els seus keys són el nom de les columnes.
- `PDO::FETCH_NUM:` array indexat que els seus keys són números.
- `PDO::FETCH_BOTH:` valor per defecte. Retorna un array indexat que els seus keys són tant el nom de les columnes com números.

<div class="center img-large">
    <img src="imagenes/06/06-pdo-listado-fetch.png">
</div>

``` php
<?php
    //  ▒▒▒▒▒▒▒▒ consulta con array asociativo.php ▒▒▒▒▒▒▒▒

    include "config/database.inc.php";

    $conexion = null;

    try{
        $conexion = new PDO(DSN, USUARIO, PASSWORD);
        $conexion -> setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

        $sql = "select * from tienda";

        $sentencia = $conexion -> prepare($sql);
        $sentencia -> setFetchMode(PDO::FETCH_ASSOC);
        $sentencia -> execute();
        
        while($fila = $sentencia -> fetch()){
            echo "Codigo:" . $fila["cod"] . "<br />";
            echo "Nombre:" . $fila["nombre"] . "<br />";
            echo "Teléfono:" . $fila["tlf"] . "<br />";
        }

    }catch(PDOException $e) {
        echo $e -> getMessage();
    }

    $conexion = null;
```

Recuperant dades amb una matriu com a resultat de la nostra consulta

``` php
<?php
    //  ▒▒▒▒▒▒▒▒ consulta con array asociativo ▒▒▒▒▒▒▒▒

    $sql="SELECT * FROM tienda";

    $sentencia = $conexion -> prepare($sql);
    $sentencia -> setFetchMode(PDO::FETCH_ASSOC);
    $sentencia -> execute();

    $tiendas = $sentencia -> fetchAll();

    foreach($tiendasas$tienda) {
        echo"Codigo:" . $tienda["cod"] . "<br />";
        echo"Nombre:" . $tienda["nombre"] . "<br />";
    }
```
Però si el que volem és llegir dades amb forma d'objecte utilitzant `PDO::FETCH_OBJ`, hem de crear un objecte amb propietats públiques amb el mateix nom que les columnes de la taula que anem a consultar.

``` php
<?php
    //  ▒▒▒▒▒▒▒▒ consulta con formato de objeto ▒▒▒▒▒▒▒▒

    $sql="SELECT * FROM tienda";

    $sentencia = $conexion -> prepare($sql);
    $sentencia -> setFetchMode(PDO::FETCH_OBJ);
    $sentencia -> execute();

    while($t = $sentencia -> fetch()) {
        echo"Codigo:" . $t -> cod . "<br />";
        echo"Nombre:" . $t -> nombre . "<br />";
        echo"Teléfono:" . $t -> tlf . "<br />";
    }
```

### Consultes amb models

Portem temps creant classes en PHP i les consultes també admeten aquest tipus de dades mitjançant l'ús de `PDO::FETCH_CLASS`

Si usem aquest mètode, hem de tindre en compte que els noms dels atributs privats han de coincidir amb els noms de les columnes de la taula que anem a manejar.

Així doncs, si pel que siga canviem l'estructura de la taula <span class="alert">**HEM DE CANVIAR**</span> la nostra classe perquè tot continue funcionant.

``` php
<?php
    //  ▒▒▒▒▒▒▒▒ clase Tienda ▒▒▒▒▒▒▒▒

    classTienda {
        private int $cod;
        private string $nombre;
        private ? string $tlf;
        
        public function getCodigo() : int {
            return $this -> cod;
        }
        
        public function getNombre() : string {
            return $this -> nombre;
        }
        
        public function getTelefono() : ?string {
            return $this -> tlf;
        }
    }
```

``` php
<?php
    //  ▒▒▒▒▒▒▒▒ Consultando a través de la clase Tienda ▒▒▒▒▒▒▒▒

    $sql = "SELECT * FROM tienda";
    $sentencia = $conexion -> prepare($sql);

    // Aquí 'Tienda' es el nombre de nuestra clase
    $sentencia -> setFetchMode(PDO::FETCH_CLASS, "Tienda");
    $sentencia -> execute();

    while($t = $sentencia -> fetch()) {
        echo "Codigo: " . $t -> getCodigo() . "<br />";
        echo "Nombre: " . $t -> getNombre() . "<br />";
        echo "Teléfono: " . $t -> getTelefono() . "<br />";
        
        var_dump($t);
    }
```

Però què passa si les nostres classes tenen constructor? doncs que hem d'indicar-li, al mètode FECTH, que emplene les propietats després de cridar al constructor i per a això fem ús de `PDO::FETCH_PROPS_LATE`.
``` php
<?php
    //  ▒▒▒▒▒▒▒▒ Consulta para una clase con constructor ▒▒▒▒▒▒▒▒

    $sql = "SELECT * FROM tienda";

    $sentencia = $conexion -> prepare($sql);
    $sentencia -> setFetchMode(PDO::FETCH_CLASS | PDO::FETCH_PROPS_LATE, Tienda::class);
    $sentencia -> execute();

    $tiendas = $sentencia -> fetchAll();
```

### Consultes amb LIKE

Per a utilitzar el comodí *LIKE* o altres comodins, hem d'associar-lo a la dada i MAI en la pròpia consulta.

``` php
<?php
    //  ▒▒▒▒▒▒▒▒ Utilizando comodines :: LIKE ▒▒▒▒▒▒▒▒

    $sql = "SELECT * FROM tienda where nombre like :nombre or tlf like :tlf";

    $sentencia = $conexion -> prepare($sql);
    $sentencia -> setFetchMode(PDO::FETCH_CLASS | PDO::FETCH_PROPS_LATE, Tienda::class);

    $cadBuscar = "%" . $busqueda . "%";

    $sentencia -> execute(["nombre" => $cadBuscar,"tlf" => $cadBuscar]);

    $result = $sentencia -> fetchAll();
```

Teniu una llista d'exemples molt completa en la [documentació oficial](https://phpdelusions.net/pdo/objects).

## Login & Password

<div class="center img-medium">
    <img src="imagenes/06/06-login-password.gif">
</div>

Per a manejar un sistema complet de login i password amb contrasenyes xifrades, necessitem un mètode que xifre aqueixos *strings* que l'usuari introdueix com a contrasenya; tant en el formulari de registre com en el del *login*, ja que en codificar una contrasenya, després hem de descodificar-la per a comprovar que totes dues *contrasenyes (la que introdueix l'usuari en el login i la que tenim en la base de dades) coincidisquen.

Necessitem doncs:

- `password_hash()` per a emmagatzemar la contrasenya en la base de dades a l'hora de fer el *INSERT*
- `PASSWORD_DEFAULT` emmagatzemem la contrasenya usant el mètode d'encriptació bcrypt

- `PASSWORD_BCRYPT` emmagatzemem la contrasenya usant l'algorisme CRYPT_BLOWFISH compatible amb crypt()

- `password_verify()` per a verificar l'usuari i la contrasenya

``` php
<?php
    //  ▒▒▒▒▒▒▒▒ Almacenando usuario y password en BD ▒▒▒▒▒▒▒▒

    $usu = $_POST["usuario"];
    $pas = $_POST["password"];

    $sql = "INSERT INTO usuarios(usuario, password) VALUES (:usuario, :password)";

    $sentencia = $conexion -> prepare($sql);

    $isOk = $sentencia -> execute([
        "usuario" => $usu,
        "password" => password_hash($pas,PASSWORD_DEFAULT)
    ]);
```

Ara que tenim l'usuari codificat i guardat en la base de dades, el recuperarem per a poder loguejar-lo correctament.
``` php
<?php
    //  ▒▒▒▒▒▒▒▒ Recuperando usuario y password en BD ▒▒▒▒▒▒▒▒

    $usu = $_POST["login"] ?? "";

    $sql = "select * from usuarios where usuario = ?";

    $sentencia = $conexion -> prepare($sql);
    $sentencia -> execute([$usu]);

    $usuario = $sentencia -> fetch();

    if($usuario && password_verify($_POST['pass'], $usuario['password'])) {
        echo"OK!";
    } else {
        echo"KO";
    }
```

## Accès a fitxers

Gràcies a la funció fopen() des de PHP podem obrir arxius que es troben en els nostres servidor o una URL.

A aquesta funció cal passar-li 2 paràmetres; el nom de l'arxiu que volem obrir i la manera en què s'obrirà

``` php
$fp = fopen("miarchivo.txt", "r");
```

Moltes vegades no podem obrir l'arxiu perquè aquest no es troba o no tenim accés a ell, per això és recomanable comprovar que podem fer-ho

``` php
if (!$fp = fopen("miarchivo.txt", "r")){
    echo "No se ha podido abrir el archivo";
}
```

### Maneres d'obertura de fitxers

- `r`: Manera lectura. Punter al principi de l'arxiu.
- `r+`: Obertura per a lectura i escriptura. Punter al principi de l'arxiu
- `w`: Obertura per a escriptura. Punter al principi de l'arxiu i el sobreescriu. Si no existeix s'intenta crear.
- `w+`: Obertura per a lectura i escriptura. Punter al principi de l'arxiu i el sobreescriu. Si no existeix s'intenta crear.
- `a`: Obertura per a escriptura. Punter al final de l'arxiu. Si no existeix s'intenta crear.
- `a+`: Obertura per a lectura i escriptura. Punter al final de l'arxiu. Si no existeix s'intenta crear.
- `x`: Creació i obertura per a només escriptura. Punter al principi de l'arxiu. Si l'arxiu ja existeix donarà error E_*WARNING. Si no existeix s'intenta crear.
- `x+`: Creació i obertura per a lectura i escriptura. Mateix comportament que x.
- `c`: Obertura per a escriptura. Si no existeix es crea. Si existeix no se sobreescriu ni dona cap error. Punter al principi de l'arxiu.
- `c+`: Obertura per a lectura i escriptura. Mateix comportament que C.
- `b`: Quan es treballa amb arxius binaris com *jpg, pdf, *png i altres. Se sol col·locar al final de la manera, és a dir *rb, r+b, x+b, *wb...

### Operacions amb arxius

Per a poder **llegir** un arxiu necessitem usar la funció *fread()* de *PHP*

```php
//  ▒▒▒▒▒▒▒▒ Abriendo un archivo y leyendo su contenido ▒▒▒▒▒▒▒▒

$file = "miarchivo.txt";
$fp = fopen($file, "r");

// filesize() nos devuelve el tamaño del archivo en cuestión
$contents = fread($fp, filesize($file));

// Cerramos la conexión con el archivo
fclose();
```

Si el que volem és **escriure** en un arxiu, haurem de fer ús de la funció *fwrite()*

```php
//  ▒▒▒▒▒▒▒▒ Escribiendo en un archivo ▒▒▒▒▒▒▒▒

$file = "miarchivo.txt";
$texto = "Hola que tal";

$fp = fopen($file, "w");

fwrite($fp, $texto);
fclose($fp);
```

### Informació d'un fitxer

Amb PHP i el seu mètode *stat()* podem obtindre informació sobre els arxius que li indiquem. Aquest mètode retorna fins a un total de 12 elements amb *informació* sobre el nostre arxiu.

0	*dev*	 número de dispositiu
1	*ino*	 número d'i-node
2	*mode*	 manera de protecció de l'i-node
3	*nlink*	 nombre d'enllaços
4	*uid*	 ID d'usuari del propietari
5	*gid*	 ID de grup del propietari
6	*rdev*	 tipus de dispositiu, si és un dispositiu i-node
7	*size*	 grandària en bytes
8	*atime*	 moment de l'últim accés (temps Unix)
9	*mtime*	 moment de l'última modificació (temps Unix)
10	*ctime*	 moment de l'última modificació de l'i-node (temps Unix)
11	*blksize*	 grandària del bloc E/S del sistema de fitxers
12	*blocks*	 nombre de blocs de 512 bytes assignats

Uns exemples...

``` php
<?php

//  ▒▒▒▒▒▒▒▒ Información del archivo ▒▒▒▒▒▒▒▒

$file = "miarchivo.txt";
$texto = "Todos somos muy ignorantes, lo que ocurre es que no todos ignoramos las mismas cosas.";

$fp = fopen($file, "w");
fwrite($fp, $texto);

$datos = stat($file);

echo $datos[3] . "<br>"; // Número de enlaces, 1
echo $datos[7] . "<br>"; // Tamaño en bytes, 85
echo $datos[8] . "<br>"; // Momento de último acceso, 1444138104
echo $datos[9] . "<br>"; // Momento de última modificación, 1444138251

?>
```

Dona una ullada a [les funcions de directoris](https://www.php.net/manual/es/book.dir.php) que té *PHP, és molt interessant.

### Arxius PDF


Amb PHP podem manejar tot tipus d'arxius com ja hem vist però, què passa si volem generar fitxers PDF amb dades tretes d'una base de dades?

<div class="center img-small">
    <img src="imagenes/06/06-pdf.png">
</div>


Gràcies a una classe escrita en PHP, podem generar arxius PDF sense necessitat d'instal·lar llibreries addicionals en el nostre servidor.

Per a això, com tenim *composer* dins de la nostra imatge de *Docker*, usarem *composer* per a instal·lar aquesta dependència.

Vegem un exemple de *Hello World* convertit a PDF

```php
<?php

ob_end_clean();
require('fpdf/fpdf.php');
  
// Instanciamos la clase
// P = Portrait | mm = unidades en milímetros | A4 = formato
$pdf = new FPDF('P','mm','A4');
  
// Añadimos una página
$pdf->AddPage();
  
// Establecemos la fuente y el tamaño de letra
$pdf->SetFont('Arial', 'B', 18);
  
// Imprimimos una celda con el texto que nosotros queramos
$pdf->Cell(60,20,'Hello World!');
  
// Terminamos el PDF
$pdf->Output();
  
?>
```
Hi ha molts exemples i tutorials, així com documentació de la classe *FPDF* en la pàgina oficial.

Visita [la secció de tutorials i el manual](http://www.fpdf.org/) per a traure major partit a aquesta classe.

```php
<?php
  
require('fpdf/fpdf.php');
  
class PDF extends FPDF {
  
    // Cabecera
    function Header() {
          
        // Añadimos un logotipo
        $this->Image('logo.png',10,8,33);
          
        // establecemos la fuente y el tamaño
        $this->SetFont('Arial','B',20);
          
        // Movemos el contenido un poco a la derecha
        $this->Cell(80);
          
        // Pintamos la celda
        $this->Cell(50,10,'Cabecera',1,0,'C');
          
        // Pasamos a la siguiente línea
        $this->Ln(20);
    }
  
    // Pie de página
    function Footer() {
          
        // Nos posicionamos a 1.5 cm  desde abajo del todo de la página
        $this->SetY(-15);
          
        // Arial italic 8
        $this->SetFont('Arial','I',8);
          
        // Número de página
        $this->Cell(0,10,'Página ' . 
            $this->PageNo() . '/{nb}',0,0,'C');
    }
}
  
// Instanciamos la clase
$pdf = new PDF();
  
// Definimos un alias para la numeración de páginas
$pdf->AliasNbPages();

$pdf->AddPage();
$pdf->SetFont('Times','',14);
  
for($i = 1; $i <= 30; $i++)
    $pdf->Cell(0, 10, 'Número de línea ' 
            . $i, 0, 1);
$pdf->Output();
  
?>
```
<div class="center img-large">
    <img src="imagenes/06/06-pdf-output.gif">
</div>

## Actividades

### PDO

601. Crea una nova base de dades amb el nom `lol` i cotejamiento de dades `utf8mb4_unicode_ci`.

602. En la nostra base de dades `lol` que acabem de crear, crearem la taula `campio` amb els següents camps.

- id [*]
- nom
- rol
- dificultat
- descripcio

Recorda't que [*] significa que és clau primària i no oblides posar el tipus de dades de cadascun dels camps.

603. Emplena la taula `campio` amb, almenys 5 registres, amb les dades que tu vulgues o si ho prefereixes, pots basar-te en la [pàgina oficial del joc](https://www.leagueoflegends.com/es-es/champions) però <span class="alert">** NO ET POSES A JUGAR !!**</span>

604. Crea l'arxiu `604.php` on llistes tots els campions del **LOL** que has ficat en la teua base de dades. Recorda't que per a això hauràs fer una connexió amb la base de dades i un `foreach` per a cada campió que tingues albergat en la taula `campio`.

605. Modifica l'arxiu `604.php` i guarda-ho com `605.php` però posa al costat de cadascun dels campions llistats un botó per a `editar` i un altre per a `esborrar`. Cadascun d'aqueixos botons farà la corresponent funció depenent de l'id del campió seleccionat.

- En punxar a editar, l'usuari serà redirigit a l'arxiu `605editant.php` on mostrarà un formulari amb els camps farcits per les dades del campió seleccionat. En donar-li al botó de `guardar` les dades es guardaran en la base de dades i l'usuari serà redirigit a la llista de *campions* per a poder veure els canvis.

- En punxar a esborrar, l'usuari serà preguntat a través d'un missatge de JavaScript (prompt) si està segur que vol esborrar al campió seleccionat. En el missatge de confirmació ha d'aparéixer el **nom del campió seleccionat**. Si l'usuari punxa a `Acceptar` el campió serà eliminat de la base de dades i l'usuari serà redirigit novament al llistat de campions per a comprovar que, efectivament aquest campió s'ha eliminat de la llista.

### Filtres i comodins

606. Modifica l'arxiu `605.php` i guarda-ho com `606.php` perquè es mostre com una taula amb les columnes de la pròpia taula de la base de dades, és a dir; id, nom, rol, dificultat, descripció. Al costat de cada nom de cada columna, posa 2 icones que siguen ˄ ˅ i que cadascun d'ells ordene el llistat en funció de quin s'haja punxat.

- Si s'ha premut en Nom la icona de ˄, el llistat ha d'aparéixer ordenat per nom ascendent. Si per contra s'ha premut ˅ haurà d'ordenar-se per nom descendent.

- Tingues en compte que cada icona ha de portar amb si un enllaç al llistat que continga paràmetres en la URL que satisfacen les opcions seleccionades així que feu ús de $_GET per a poder capturar-los i escriviu les consultes SQL que siguen necessàries per a fer cadascun dels filtres.

- Pots usar [Font Awesome](https://fontawesome.com) per a les icones però és una cosa opcional

607. Crea una taula nova dins de la base de dades `lol` que ja tens i crea un sistema de login amb usuaris. Introdueix en la base de dades almenys 3 usuaris diferents amb les seues contrasenyes diferents. Recorda que:

- La taula nova ha de dir-se `usuari`

- Els camps a crear en la nova taula han de ser

- `id` [*]
- `nom`
- `usuari`
- `password`
- `email`

- Les contrasenyes han de ser xifrades abans de guardar el dades en la base de dades.

- Crea el formulari `607.php` on l'usuari introduïsca les dades de registre i vincula'l amb `607nuoUsuari.php` perquè reculla les dades mitjançant POST i els inserisca en la base de dades si tot ha anat bé.

- Queda <span class="alert">**PROHIBIDÍSSIM**</span> accedir a `607nouUsuari.php` sense el formulari emplenat.

- La sentència de **INSERT** ha d'estar controlada perquè no puga introduir-se cap dada en blanc. Tingues en compte que estàs modificant la base de dades i no volem camps mal emplenats.

- Si tot ha anat bé, mostra un missatge per pantalla dient `L'usuari XXX ha sigut introduït en el sistema amb la contrasenya YYY`.

### Fitxers

609. Fica't en [loremipsum.com](https://www.lipsum.com/) i genera un text de 3 paràgrafs. Còpia el text generat i guarda'l en un arxiu nou anomenat `*609.txt`. Genera un arxiu php anomenat `609.php` i mostra per pantalla el text de l'arxiu txt que acabes de crear, la seua grandària en **Kilobytes** , la data de la seua última modificació i l'id d'usuari que va crear l'arxiu.

610. Torna a carregar l'arxiu `606.php` i canvia-ho de nom a `610.php` però en comptes de mostrar la taula per pantalla, genera un arxiu CSV `610.csv` i un altre `610CSV.php` on mostres per pantalla el contingut de l'arxiu `610.csv`.

### Projecte CholloSevero

<div class="center img-large">
    <img src="imagenes/06/06-chollometro.gif">
</div>

Anem A treballarem amb una base de dades on emmagatzemarem la informació relacionada amb ofertes que publiquen els usuaris i els llistarem en funció de diversos filtres; nous, més votats, més vistos, més comentats entre altres, al més pur estil **[Chollometro](https://www.chollometro.com/)**.

615. Estructura el Projecte i pensa en les taules i bases de dades que necessiteu per a crear el Projecte. 

616. Crea un sistema de login/password amb els rols `administrador` i `usuari`. De moment que es validen els usuaris correctament utilitzant encriptació en la contrasenya.

- `Administrador`: Pot veure tots els usuaris registrats així com els administradors i les gangues creades en la base de dades.

- `Usuari`: Pot veure les seues pròpies gangues, editar-los i esborrar-los, a més de crear nous.

617. Crea la vista per a posar noves gangues i recorda <span class="alert">només poden entrar a aquesta vista usuaris registrats o administradors</span>.

618. Crea la vista on es mostren totes les gangues creades. Aquesta vista pot veure-la qualsevol usuari, registrat o no en el sistema. Tingues en compte que aquesta vista serà la vista general de la web així que pots cridar-la `index.php` on després aplicarem filtres per $_GET.