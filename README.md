# Kelboy_2.X
Directorio con los releases de la kelboy2.X


#Instalación

Una vez tenemos metidas todas las carpetas en el sistema devemos ejecutar el 

`sudo depmod -a 
`

Esto es necesario para que linkee el Kernel los nuevos drivers, así mismo debemos activar el nuevo kernel en el config.txt

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



Una realizado y terminado los procesos anteriormente descritos, apagaremos la kelboy 2.X para meter la SD en nuestro PC y copiar las carpetas etc, usr y lib.