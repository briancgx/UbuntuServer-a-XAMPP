# Integración de Ubuntu Server con XAMPP en Windows

Este repositorio contiene instrucciones detalladas para enlazar un servidor Ubuntu con un entorno XAMPP en una máquina con Windows, este enlace unicamente será con los servicios de MySQL y Apache2, funcionará como un servidor espejo.

## Importancia de un servidor espejo
Un servidor espejo, en el contexto de sistemas informáticos y redes, es una réplica exacta de otro servidor que se utiliza para mejorar la disponibilidad, la escalabilidad y la redundancia de los servicios ofrecidos. El servidor espejo replica los datos y configuraciones del servidor principal de manera regular y automática, de modo que si el servidor principal falla o se vuelve inaccesible, el servidor espejo puede tomar su lugar y continuar ofreciendo los servicios sin interrupción. Esto ayuda a garantizar la continuidad del servicio y a reducir los tiempos de inactividad en caso de fallos. Los servidores espejo también se utilizan a menudo para distribuir cargas de trabajo, mejorar el rendimiento y proporcionar una mayor capacidad de procesamiento.

## Requisitos previos

- Ubuntu Server instalado.
- XAMPP instalado en una máquina con Windows.
- Conexión de red entre Ubuntu Server y la máquina con Windows.

## Pasos a seguir

1. **Instalación y configuración de XAMPP en Windows**
   - Descarga [XAMP](https://www.apachefriends.org/index.html).
   - Ejecuta el instalador y sigue las instrucciones.
   - Asegúrate de que los servicios de Apache y MySQL no estén en ejecución por ahora.

2. **Configuración de red**
   - Configura la red para permitir la comunicación entre Ubuntu Server y la máquina con Windows.
   - Asegúrate de que el firewall en la máquina con Windows permita la conexión desde Ubuntu Server.

3. **Configuración en Ubuntu Server**
   - Instala el cliente MySQL en Ubuntu Server si aún no lo has hecho:
     ```bash
     sudo apt-get install mysql-client
     ```
   - Conéctate a la base de datos MySQL en XAMPP desde Ubuntu Server: `mysql -h <IP_de_la_máquina_con_Windows> -u <usuario> -p`.

## Notas importantes

- Este proceso asume que ambos sistemas están en la misma red local.
- Asegúrate de configurar adecuadamente la seguridad de tu base de datos y red para evitar accesos no autorizados.

## Contribución

¡Si deseas contribuir a este repositorio, envía tus solicitudes de extracción y reportes de problemas!

## Licencia

Este proyecto está bajo la [Licencia MIT](LICENSE).

## Contacto

Para cualquier pregunta o problema, contáctame en [tu-email@example.com](mailto:tu-email@example.com).
