# Integración de Ubuntu Server con XAMPP en Windows

Este repositorio contiene instrucciones detalladas para enlazar un servidor Ubuntu con un entorno XAMPP en una máquina con Windows, este enlace unicamente será con los servicios de MySQL y Apache2, funcionará como un servidor espejo.

## Importancia de un servidor espejo
Un servidor espejo, en el contexto de sistemas informáticos y redes, es una réplica exacta de otro servidor que se utiliza para mejorar la disponibilidad, la escalabilidad y la redundancia de los servicios ofrecidos. El servidor espejo replica los datos y configuraciones del servidor principal de manera regular y automática, de modo que si el servidor principal falla o se vuelve inaccesible, el servidor espejo puede tomar su lugar y continuar ofreciendo los servicios sin interrupción. Esto ayuda a garantizar la continuidad del servicio y a reducir los tiempos de inactividad en caso de fallos. Los servidores espejo también se utilizan a menudo para distribuir cargas de trabajo, mejorar el rendimiento y proporcionar una mayor capacidad de procesamiento.

## Requisitos previos

- Ubuntu Server instalado.
- XAMPP instalado en una máquina con Windows.
- Conexión de red entre Ubuntu Server y la máquina con Windows.

## Pasos a seguir

1. **Instalación de XAMPP en Windows**
   - Descarga [XAMP](https://www.apachefriends.org/index.html).
   - Ejecuta el instalador y sigue las instrucciones.
   - Asegúrate de que los servicios de Apache y MySQL no estén en ejecución por ahora.

2. **Configuración de red**
   - Configura la red para permitir la comunicación entre Ubuntu Server y la máquina con Windows.
   - Asegúrate de que el firewall en la máquina con Windows permita la conexión desde Ubuntu Server.

3. **Configuración de MariaDB en Ubuntu Server**
   - Instala el cliente de MariaDB.
     ```bash
     sudo apt install mariadb-server
     ```
   - Conéctate a la base de datos MySQL.
     ```bash
     mariadb -u root -p
     ```
   - Creamos el usuario con el que ingresaremos a la db en ambos servidores.
      ```bash
     CREATE USER 'test'@'localhost' IDENTIFIED BY 'test';
     CREATE USER 'test'@'%' IDENTIFIED BY 'test';
     ```
   - Otorgamos todos los privilegios para no tener problemas con la conexión.
     ```bash
     GRANT ALL PRIVILEGES ON *.* TO 'test'@'localhost' WITH GRANT OPTION;
     GRANT ALL PRIVILEGES ON *.* TO 'test'@'%' WITH GRANT OPTION;
     ```
   - Recargamos los privilegios
     ```bash
     FLUSH PRIVILEGES;
     ```     
   - Verificamos los privilegios otorgados a nuestro usuario.
     ```bash
     SHOW GRANTS FOR 'test'@'localhost';
     SHOW GRANTS FOR 'test'@'%';
     ```
   - Debemos ver algo similar a esto:
     ```
     GRANT ALL PRIVILEGES ON *.* TO 'test'@'localhost' WITH GRANT OPTION
     GRANT ALL PRIVILEGES ON *.* TO 'test'@'%' WITH GRANT OPTION
     ```
   - Una vez creado y configurado el usuario podemos salirnos de MySQL.
     ```bash
     exit
     ```
   - Ahora dentro de `/etc/mysql/my.cnf` y `/etc/mysql/mariadb.cnf` añadiremos estas lineas de codigo, justo al final de ambos archivos.
     ```bash
     [mysqld]
     bind-address = 0.0.0.0
     ```
   - 

4. **Configuración de Apache en Ubuntu Server**
   - Instala Apache y las utilidades para usarlo con PHP.
     ```bash
     sudo apt install apache2
     sudo apt install php
     sudo apt install libapache2-mod-php
     sudo apt-get install php-mysqli
     ```
   - Reiniciamos el servicio de Apache para evitar posibles errores.
     ```bash
     sudo systemctl restart apache2
     ```
   - Abrimos el archivo `/etc/apache2/apache2.conf` y añadimos estas lineas de codigo al final.
     ```bash
     <FilesMatch \.php$>
        SetHandler application/x-httpd-php
     </FilesMatch>
     ```
5. **Configuración de Samba en Ubuntu Server para compartir la carpeta del sitio web**
   - Instalamos Samba.
     ```bash
     sudo apt install samba
     ```
   - Añadimos las siguientes lineas de codigo al archivo de configuración `/etc/samba/smb.conf`
     ```bash
     [htdocs]
     path = /var/www/html
     available = yes
     valid users = test
     read only = no
     browsable = yes
     public = yes
     writable = yes
     ```
   - Añadimos nuestro usuario de Ubuntu a Samba ejecutando el siguiente comando, y despues añadiendo la contraseña.
     ```bash
     sudo smbpasswd -a test
     ```
   - Reinciamos el servicio de Samba.
     ```bash
     sudo systemctl restart smbd
     ```

6. **Revisamos la ip de Ubuntu Server antes de comenzar a configurar el XAMPP de Windows**
   - Ejecutamos el comando.
     ```bash
     ip a
     ```
7. **Realizamos la configuración de Windows**
   - Primero vamos a enlazar la carpeta previamente compartida con Samba, entramos al `Explorador de archivos`, seguido haciendo click derecho sobre `Este Equipo` y `Conectar a unidad de red` para tener la ventana emergente.
   
![image](https://github.com/briancgx/UbuntuServer-a-XAMPP/assets/118696146/2cb6c544-542c-40cb-afd6-5070f036423f)

   - Dentro del campo de Carpeta, vamos a ingresar la ip de Ubuntu Server que obtuvimos antes y la carpeta compartida y presionamos Finalizar.
     
![image](https://github.com/briancgx/UbuntuServer-a-XAMPP/assets/118696146/d771999e-973f-4a20-8009-daf03407abb8)

   - Usamos las credenciales ya antes creadas del usuario `test`.
     
![image](https://github.com/briancgx/UbuntuServer-a-XAMPP/assets/118696146/06826985-4d7e-41ee-9de8-b2eeb4430725)

   - Si todo salio bien debemos poder acceder a la unidad compartida y se verá de esta manera.
     
![image](https://github.com/briancgx/UbuntuServer-a-XAMPP/assets/118696146/24ebf7b2-4c16-4d7d-b130-0bf5b5456a66)

   - Dentro de esta carpeta compartida podemos añadir nuestros archivos html y php para visualizar en la web, en mi caso cree los siguientes `index.html` y `mostrar_datos.php`.
     ```html
     <!DOCTYPE html>
      <html lang="es">
      <head>
          <meta charset="UTF-8">
          <meta name="viewport" content="width=device-width, initial-scale=1.0">
          <title>SERVIDOR ESPEJO</title>
          <style>
              body {
                  font-family: Arial, sans-serif;
                  background-color: hsl(277, 74%, 80%);
                  margin: 0;
                  padding: 0;
                  display: flex;
                  justify-content: center;
                  align-items: center;
                  height: 100vh;
              }
      
              .container {
                  background-color: #ffffff;
                  padding: 20px;
                  border-radius: 8px;
                  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
                  max-width: 600px;
                  text-align: center;
              }
      
              h1 {
                  color: #333;
              }
      
              p {
                  color: #555;
                  line-height: 1.6;
              }
      
              .button {
                  display: inline-block;
                  margin-top: 20px;
                  padding: 10px 20px;
                  color: #ffffff;
                  background-color: #007BFF;
                  text-decoration: none;
                  border-radius: 5px;
                  transition: background-color 0.3s;
              }
      
              .button:hover {
                  background-color: #0056b3;
              }
          </style>
      </head>
      <body>
          <div class="container">
              <h1>SERVIDOR ESPEJO</h1>
              <p>Un servidor espejo, también conocido como "mirror server", es un servidor que mantiene una copia exacta de otro servidor. Su principal función es proporcionar redundancia y aumentar la disponibilidad de los datos al distribuir la carga de acceso entre varios servidores.</p>
              <p>Los servidores espejo se utilizan principalmente para distribuir contenido estático como archivos de software, imágenes, videos y otros datos que requieren una alta disponibilidad y accesibilidad. Al tener múltiples servidores con el mismo contenido, se puede reducir la carga en el servidor principal y mejorar la experiencia del usuario al proporcionar tiempos de respuesta más rápidos.</p>
              <a href="mostrar_datos.php" class="button">Ver tablas</a>
          </div>
      </body>
      </html>
     ```
     
     ```php
      <!DOCTYPE html>
      <html>
      <head>
          <title>Mostrar Datos</title>
          <style>
              table {
                  width: 100%;
                  border-collapse: collapse;
              }
              table, th, td {
                  border: 1px solid black;
              }
              th, td {
                  padding: 10px;
                  text-align: left;
              }
              th {
                  background-color: #f2f2f2;
              }
          </style>
      </head>
      <body>
      
      <h2>Datos de la Base de Datos</h2>
      
      <?php
      // Mostrar errores de PHP
      ini_set('display_errors', 1);
      ini_set('display_startup_errors', 1);
      error_reporting(E_ALL);
      
      // Datos de conexión
      $servername = "127.0.0.1";
      $username = "test"; // Usuario de la base de datos
      $password = "test"; // Contraseña del usuario antes creado para la base de datos
      $dbname = "datos"; // Nombre correcto de la base de datos
      
      // Crear la conexión
      $conn = new mysqli($servername, $username, $password, $dbname);
      
      // Verificar la conexión
      if ($conn->connect_error) {
          die("Connection failed: " . $conn->connect_error);
      } else {
          echo "Conexión exitosa<br>";
      }
      
      // Consulta SQL para obtener los datos
      $sql = "SELECT * FROM alumnos"; // Verificar el nombre correcto de la tabla
      $result = $conn->query($sql);
      
      if (!$result) {
          die("Error en la consulta: " . $conn->error);
      }
      
      if ($result->num_rows > 0) {
          // Mostrar los datos en una tabla HTML
          echo "<table><tr><th>Nombre</th><th>Edad</th></tr>";
          while($row = $result->fetch_assoc()) {
              echo "<tr><td>".$row["Nombre"]."</td><td>".$row["Edad"]."</td></tr>";
          }
          echo "</table>";
      } else {
          echo "0 results";
      }
      
      // Cerrar la conexión
      $conn->close();
      ?>
      
      </body>
      </html>
     ```
   - Ahora ubicamos el archivo `httpd.conf`, en mi caso se encuentra en la ruta `C:\xampp\apache\conf` y lo abrimos con Bloc de Notas, modificaremos las lineas que se ven de esta manera:
```text
DocumentRoot "C:/xampp/htdocs"
<Directory "C:/xampp/htdocs">
```
Las reemplazaremos por estas (IMPORTANTE: Revisar la ruta de la carpeta compartida, en mi caso el disco es Z, por esp se ve de esa forma):
```text
DocumentRoot "Z:/"
<Directory "Z:/">
```
   - Ahora instalaremos [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) para la conexión a la base de datos, ingresando al link y ejecutando el archivo que se haya descargado.

   - Una vez instalado abriremos PuTTY y presionamos `+` en la opcion de SSH a la izquierda, despues seleccionamos `Tunnels` y añadimos lo siguiente:
     En `Source Port` pondremos el puerto del mysql, que es el `3306`
     En `Destination` pondremos `127.0.0.1:3306` que indica el servidor local.
     Después de añadir eso, presionamos `Add`.
     
 ![image](https://github.com/briancgx/UbuntuServer-a-XAMPP/assets/118696146/10d4f702-6861-4ed8-8145-4a58f664a6b3)
 
   - Ahora en el mismo menú de la izquierda presionaremos `Session` y modificamos:
     En `Host Name (or IP address)` pondremos la ip de nuestro Ubuntu Server.
     En `Saved Sessions` pondremos un nombre identificativo y luego presionamos `Save`
     
![image](https://github.com/briancgx/UbuntuServer-a-XAMPP/assets/118696146/d4c70b32-1f42-40aa-9847-8d16b86dcd90)

   Finalmente Seleccionamos dentro de las sesiones la que recien creamos y presionamos `Open`, nos abrirá un menú y solo presionamos `Accept`.
     
![image](https://github.com/briancgx/UbuntuServer-a-XAMPP/assets/118696146/a8f78931-01c5-41a2-a456-12bb3dd6bc84)

![image](https://github.com/briancgx/UbuntuServer-a-XAMPP/assets/118696146/caa4c9e7-b049-48d8-89bf-bb77f0432e3f)

      Ahora ingresamos nuestras credenciales de Ubuntu Server, como por SSH y estaremos logueados, solo minimizaremos esa pestaña.
      
![image](https://github.com/briancgx/UbuntuServer-a-XAMPP/assets/118696146/cc093bdb-559e-401d-87d7-e52bdff41043)

   - Una vez finalizada la conexion de PuTTY, ubicaremos el archivo `config.inc.php`, en mi caso esta en la carpeta `C:\xampp\phpMyAdmin` y vamos a dejarlo de la siguiente manera:
   ```php
   <?php
   /*
    * This is needed for cookie based authentication to encrypt password in
    * cookie
    */
   $cfg['blowfish_secret'] = 'xampp'; /* YOU SHOULD CHANGE THIS FOR A MORE SECURE COOKIE AUTH! */
   
   /*
    * Servers configuration
    */
   $i = 0;
   
   /*
    * First server
    */
   $i++;
   
   /* Authentication type and info */
   $cfg['Servers'][$i]['auth_type'] = 'config';
   $cfg['Servers'][$i]['user'] = 'test'; // Usuario de la db
   $cfg['Servers'][$i]['password'] = 'test'; // Contraseña de la db
   $cfg['Servers'][$i]['extension'] = 'mysqli';
   $cfg['Servers'][$i]['AllowNoPassword'] = true;
   $cfg['Lang'] = '';
   
   /* Bind to the localhost ipv4 address and tcp */
   $cfg['Servers'][$i]['host'] = '127.0.0.1';  // El extremo local del túnel SSH
   $cfg['Servers'][$i]['connect_type'] = 'tcp';
   
   /* User for advanced features */
   $cfg['Servers'][$i]['controluser'] = 'pma';
   $cfg['Servers'][$i]['controlpass'] = '';
   
   /* Advanced phpMyAdmin features */
   $cfg['Servers'][$i]['pmadb'] = 'phpmyadmin';
   $cfg['Servers'][$i]['bookmarktable'] = 'pma__bookmark';
   $cfg['Servers'][$i]['relation'] = 'pma__relation';
   $cfg['Servers'][$i]['table_info'] = 'pma__table_info';
   $cfg['Servers'][$i]['table_coords'] = 'pma__table_coords';
   $cfg['Servers'][$i]['pdf_pages'] = 'pma__pdf_pages';
   $cfg['Servers'][$i]['column_info'] = 'pma__column_info';
   $cfg['Servers'][$i]['history'] = 'pma__history';
   $cfg['Servers'][$i]['designer_coords'] = 'pma__designer_coords';
   $cfg['Servers'][$i]['tracking'] = 'pma__tracking';
   $cfg['Servers'][$i]['userconfig'] = 'pma__userconfig';
   $cfg['Servers'][$i]['recent'] = 'pma__recent';
   $cfg['Servers'][$i]['table_uiprefs'] = 'pma__table_uiprefs';
   $cfg['Servers'][$i]['users'] = 'pma__users';
   $cfg['Servers'][$i]['usergroups'] = 'pma__usergroups';
   $cfg['Servers'][$i]['navigationhiding'] = 'pma__navigationhiding';
   $cfg['Servers'][$i]['savedsearches'] = 'pma__savedsearches';
   $cfg['Servers'][$i]['central_columns'] = 'pma__central_columns';
   $cfg['Servers'][$i]['designer_settings'] = 'pma__designer_settings';
   $cfg['Servers'][$i]['export_templates'] = 'pma__export_templates';
   $cfg['Servers'][$i]['favorite'] = 'pma__favorite';
   
   /*
    * End of servers configuration
    */
   ?>
   ```
8. **Probamos el resultado encendiendo XAMPP**
   - Una vez realizada toda la configuración, podemos encender los servicios de `Apache` y `MySQL` en XAMPP, fijandonos que ambos servicios se vean en verde.
     
![image](https://github.com/briancgx/UbuntuServer-a-XAMPP/assets/118696146/3a23e62a-8033-4573-b380-de239e796faf)

   - Accedemos a cualquier navegador al `localhost` y la ip de la maquina Ubuntu Server (En mi caso `192.168.1.107`). En ambas paginas web debemos tener el mismo resultado, y al presionar en `Ver tablas` debemos poder mirar la base de datos enlazada, a la cual podemos añadir o quitar datos dentro del sistema y ahi se verá afectado.
     
![image](https://github.com/briancgx/UbuntuServer-a-XAMPP/assets/118696146/35eb3eb8-dd7a-4008-b229-9ca5bf94c20d)

![image](https://github.com/briancgx/UbuntuServer-a-XAMPP/assets/118696146/e51be924-ca42-41e9-8255-0348564293f1)


## Notas importantes

- Este proceso asume que ambos sistemas están en la misma red local.
- Asegúrate de configurar adecuadamente la seguridad de tu base de datos y red para evitar accesos no autorizados.

© briancgx
