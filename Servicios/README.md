#Instalacion de servicio SystemAPP 
---
**Deprecated --> Ahora esto lo lleva el launcher**
  
---

Para poder funcionar debemos instalar un servicio dentro de linux en los runlevel, la idea de esto es que se ejecute
al iniciar y al apagar el sistema, para ello nos vamos a ayudar en el chkconfig.
`
sudo apt install chkconfig
`
El script debe tener permisos de ejecucion y estar instalado en /etc/init.d/

Una vez ahí, tenemos que hacer un 
`
sudo chkconfig kelboy_systemAPP on
`
Con esto ya tenemos el servicio creado y funcionando correctamente.

**NOTA:** 

Por último ejecutar esto obligatoriamente.
`
sudo update-rc.d kelboy_systemAPP defaults
`


# Instalación de servicio para mantener la APP de la kelboy siempre funcional. 
---
**Deprecated --> Ahora esto lo lleva el launcher**

---

Una vez finalizado esto.... debemos crear en el systemd una entrada para que si el script sale de manera abrupta pueda reinicializrse

Con el siguiente codigo si tenemos una salida abrupta o un fallo... se reinicializara el servicio llamando al apartado restart)
```{c}
sudo systemctl edit kelboy_systemAPP

[Service]
PIDFile=/var/run/kelboy_systemAPP.pid
Restart=alwaysMarkdown All in One
RemainAfterExit=no
```
Una vez finalizado si no nos arranca podemos hacerlo manualmente
`
sudo systemctl start kelboy_systemAPP
`

# Comando de apagado al hardware una vez estemos cerrando el sistema.
--- 
##[Necesario] Kelboy2.1/Kelboy 2.0
---

Posteriormente vamos a crer el servicio que es dependiente del shutdown, para evitar que se nos apague la pcb en un reinicio, para ello creamos el servicio i2c_shutdown
` 
sudo nano /etc/systemd/system/i2c_shutdown.service
`

```{c}

[Unit]
Description=I2C kelboy2.0 HW shutdown
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

#Servicio para el control del FAN

Igual que en el servicio anterior debemos crear el archivo de servicio para ello


`
sudo nano /etc/systemd/system/fan_kelboy.service
`

Posteriormente debemos escribir dentro del archivo lo siguiente.

```{c}
[Unit]
Description=Fan control service Kelboy
After=network.target

[Service]
ExecStart=/usr/bin/bash /home/pi/kelboy-launcher/bin/fan.sh
Restart=always

[Install]
WantedBy=multi-user.target

```

Por último lo habilitamos si tenemos un fan de la manera :

`
sudo systemctl enable fan_kelboy.service
`

Para hacer que este servicio se arranque tenemos dos opciones, habilitarlo manualmente o reinicar la consola, la manera de habilitarlo manualmente es:

`
sudo systemctl start fan_kelboy.service
`
