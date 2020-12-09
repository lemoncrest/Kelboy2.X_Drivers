# Kelboy_2.X
Directorio con los releases de la kelboy2.X

Este hardware necesita un total de 3 drivers, WiFi, BT y Fuel Gauge por I2C.


# Instalación Guía

Para poder proceder con esta instalación de los archivos y configuraciones necesitamos una imagen nueva y un teclado.

El primer paso es copiar solo los archivos /boot/config.txt y /boot/dtblob.bin a nuestra partición /boot/ de la imagen que acabamos de descargar. De esta manera tendremos pantalla visible en nuestra Kelboy 2.X.

Ahora la imagen hará lo oportuno en su primer arranque, el resize y demás que tenga configurado.

**Opcional**
Para poder poner el teclado de manera adecuada para poderse utilizar debemos realizar 
`sudo nano /etc/default/keyboard
`

A sú vez, deberemos poner el autologin, para esto haremos 
`sudo raspi-config
`
En la línea número 1, luego a la 5, para, por último pulsar en la línea 2. Nos pedirá reiniciar, le decimos que sí. Ahora ya tenemos lista nuestra imagen base para poder instalar los drivers de la kelboy.

Aquí debemos cambiar la línea **XKBLAYOUT="esto_a_nuestra_region"**, de manera que para que esté en español el teclado en el siguiente reinicio debería quedar como **XKBLAYOUT="es"**

**Fin Opcional**



Una realizado y terminado los procesos anteriormente descritos, apagaremos la kelboy 2.X con el botón y el comando 
`sudo shuthdown -h now
`
 para meter la SD en nuestro PC y copiar las carpetas etc, usr y lib.

Aquí tenemos dos opciones, o las copiamos a manos con un gestor de ficheros con privilegios de administrador o desde consola de comandos. Para ello nos colocamos con cd dentro del directorio **Kelboy5.4.77-v7+** que contiene los drivers y ejecutamos el siguiente comando:

`sudo rsync -auvh etc/ /media/ubuntu/rootfs/etc
 sudo rsync -auvh lib/ /media/ubuntu/rootfs/lib/
 sudo rsync -auvh usr/ /media/ubuntu/rootfs/usr/
 sudo rsync -auvh boot/ /media/ubuntu/boot/
`


En este punto debemos modificar el /boot/config.txt para que arranque nuestro kernel compilado, en este caso es el Kernel5_4_77.img, de manera que la línea quede como sigue:

`kernel=Kernel5_4_77.img`

Con esto le decimos a raspberry que la próxima vez que inicie el dispositivo, utilice nuestro kernel así como los modulos que hemos compilado.

Ahora debemos hacer que el kernel recoja los drivers con  `sudo depmod-a`, posteriormente debemos crear el servicio de sistema que hará que nuestra kelboy sepa cuando se apaga el dispositivo.



` 
sudo nano /etc/systemd/system/i2c_shutdown.service
`
Posteriormente copiamos este contenido 

```{c}

[Unit]
Description=I2C kelboy2.X HW shutdown
DefaultDependencies=no
Before=poweroff.target halt.target

[Service]
Type=oneshot
ExecStart=/usr/sbin/i2cset -y 1 0x0a 0x20

[Install]
WantedBy=poweroff.target halt.target
```

Una vez finalizado debemos activarlo

`
sudo systemctl enable i2c_shutdown.service
`

Ahora debemos activar el SSH y el I2C, para ello lo realizaremos desde el `sudo raspi-config`
Por último instalaremos el i2ctools con `sudo apt install i2c-tools`, con el que podremos comprobar que la kelboy está trabajando adecuadamente con el comando `sudo i2cdetect -y 1` que nos debe dar el resultado siguiente:

     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:          -- -- -- -- -- -- -- 0a -- -- -- -- --
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
30: -- -- -- -- -- -- UU -- -- -- -- -- -- -- -- --
40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
50: UU UU -- -- -- -- -- -- -- -- -- -- -- -- -- --
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
70: -- -- -- -- -- -- -- --

El driver Fuel Gauge está en la posición 36, la memoria EEPROM en la 50/51 y el arduino que controla el hardware en el 0A.

Por último, debemos crear el archivo de sonido, con este lo que ganamos es un control software para el volumen con ayuda del dmixer de alsa `nano ~/.asoundrc`. **Para que funcione correctamente y veamos el cambio en alsamixer, algún elemento de audio tiene que intentar reproducir algún sonido, ya que de esta manera ejecuta el proceso que nos crea este control software. Luego en alsamixer podremos manejarlo**


```{c}
cm.kelboy {
   type hw card 0
}
pcm.dmixer {
   type dmix
   ipc_key 1024
   ipc_perm 0666
   slave {
     pcm "kelboy"
     period_time 0
     period_size 1024
     buffer_size 8192
     rate 44100
     channels 2
   }
}

ctl.dmixer {
    type hw card 0
}


pcm.SoftVolume {
        type    softvol
        slave.pcm       "dmixer"
        control.name    "Master"
        control.card    0
}

ctl.softvol{
        type hw card 0
}

pcm.!default {
        type plug
        slave.pcm "SoftVolume"
}


Con esto tendríamos nuestra imagen con el último Kernel sacado por Lemoncrest SL.

En este punto ya solo quedaría configurar la WiFi. Podemos hacerlo colocando un archivo wpa_supplicant.conf en la partición /boot/ con los siguientes datos

`country=es
update_config=1
ctrl_interface=/var/run/wpa_supplicant

network={
 scan_ssid=1
 ssid="El_nombre_de_tu_wifi_exacta_como_la_ves_en_el_pc"
 psk="contraseña"
}
`

o mediante `sudo raspi-config`.





