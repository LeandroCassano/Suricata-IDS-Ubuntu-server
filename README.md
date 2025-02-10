# Suricata como IDS -Ubuntu-server
El fin de este informe es mostrar la implementacion de un IDS Suricata dentro de un entorno ubuntu server, realizar ataques y verificar su funcionalidad. 
Para ello voy a ocupar un Kali linux que va a servir de herramienta de ataque, ambas maquinas (Ubuntu server y Kali linux) van a estar siendo virtualizadas en VMware

\\ 

Ubuntu server 24.04 




![Texto alternativo](https://github.com/LeandroCassano/Suricata-Ubuntu-server/blob/main/ubuntu%20server.JPG)




Por comodidad creo una sesión ssh para poder gestionar el servidor desde mi maquina linux




![](https://github.com/LeandroCassano/Suricata-Ubuntu-server/blob/main/1%20conexion%20ssh%20de%20kali%20a%20ubuntu.JPG)


Después de instalar suricata y de su respectiva actualización de paquetes, paso a la configuración del YAML


¿Que es un YAML?
  Yaml (YAML Ain't Markup Language) es un lenguaje de serialización de datos, se usa para representar y estructurar datos de manera legible, comúnmente en configuraciones de aplicaciones, despliegues de infraestructura y automatización de procesos.


En este caso el archivo de configuración general de la herramienta suricata (Suricata.yaml) viene en este formato de archivo y nos sirve para las Interfaces de red a monitorear, Logs y formatos de salida, umbrales y filtros de alertas, configuración de reglas y detección de protocolos entre otras funciones.





![Suricata.yaml](https://github.com/LeandroCassano/Suricata-Ubuntu-server/blob/main/4%20suricata%20yaml.JPG)






Vamos a desglosar lo mas significativo ha configurar:

Configurar las interfaces de red que Suricata debe monitorear
En la sección de interfaces, se define qué interfaces de red deben ser monitoreadas por Suricata. Esto es útil si tenes múltiples interfaces o si queres monitorear una específica.

Define las interfaces que Suricata debe usar para la captura de paquetes af-packet: - interface: eth0
 threads: auto


 Configurar los logs y los formatos de salida, modificar la seccion de logs para definir que informacion quiero que suricata registre y el formato en el que se almacena
 Log de eventos en formato JSON
output:
  - file:
      enabled: yes
      filename: /var/log/suricata/eve.json
      append: yes

Ajustar las reglas de detección
Suricata utiliza reglas para detectar tráfico malicioso. Puedes habilitar, deshabilitar o modificar las reglas que se cargan. Esta configuración puede estar en la sección de rule-files
Esto es importante ya que aca es donde especificamos la ruta del archivo o archivos que suricata va a tener en cuenta a la hora de aplicar las reglas.

Una vez configurado el Yaml paso a realizar el chequeo del mismo, este comando es tan simple como importante. “sudo suricata -T -c /etc/suricata/suricata.yaml” Esto lo que va a ser es ejecutar suricata en modo test, lo que significa que estará capturando paquetes solo verifica el archivo.
Revisará que no haya errores de sintaxis,validar configuraciones específicas, prevenir caídas del servicio, asegurar compatibilidad con las reglas y facilitar la depuración.




![](https://github.com/LeandroCassano/Suricata-Ubuntu-server/blob/main/5%20verificacion%20de%20archivos.JPG)





Una vez que tenemos configurado y corriendo suricata vamos a pasar a probar su funcionalidad. Voy a usar apache con un ejemplo frecuente y muy visto en servidores web que es una denegación de servicio (DoS) realizada desde Kali linux para comprobar su funcionalidad. 
Pero antes que eso vamos a configurar el archivo de reglas “etc/suricata/rules”





Teniendo todo listo vamos a revisar los logs para verificar el ataque. Hay muchas maneras de hacer esto, pero voy a hacerlo de manera básica solo de ejemplo.
Este comando permite ver en tiempo real los logs de la herramienta en la misma terminal tail -f /var/log/suricata/suricata.log


Instalamos y dejamos funcional Apache. Ahora para la denegación de servicio voy a usar Hping, la herramienta Hping es usada generalmente para auditorias de seguridad gracias al poder de manipular paquetes y generar ataques DoS para testear la resistencia de servidores que nos brinda.




![](https://github.com/LeandroCassano/Suricata-Ubuntu-server/blob/main/Ataque%20ddos.JPG)




En este ataque Suricata detectó anomalías en los paquetes TCP enviados al puerto 80 del servidor, como ACKs inválidos y cierres incorrectos de conexiones (RST). En su configuración por defecto, Suricata identifica, clasifica y registra estos eventos sin bloquear el tráfico, pero genera logs detallados que permiten monitorear el ataque en tiempo real y, si se integra con un firewall, tomar medidas correctivas.
