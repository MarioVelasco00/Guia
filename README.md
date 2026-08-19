### 1. Eliminar Apache y limpiar el sistema
Pon este comando:
```text
sudo systemctl stop apache2
```
Luego este:
```text
sudo apt purge apache2 -y && sudo apt autoremove -y
```

---

### 2. Cambiar el nombre del servidor a ha2
Pon este comando:
```text
sudo hostnamectl set-hostname Lab2-2-servidor-ha2
```
Luego refresca la terminal con:
```text
exec bash
```

---

### 3. Instalar Nginx, Keepalived, Rsync y SSH
Pon este comando:
```text
sudo apt update && sudo apt install nginx keepalived rsync openssh-server -y
```

---

### 4. Personalizar la página web
Pon este comando:
```text
sudo bash -c 'echo "<h1>Servidor 2: BACKUP (Estudiante B)</h1>" > /var/www/html/index.nginx-debian.html'
```

---

### 5. Crear el script de monitoreo
Pon este comando para crear el archivo:
```text
sudo bash -c 'cat << "EOF" > /etc/keepalived/check_nginx.sh
#!/bin/bash
systemctl status nginx > /dev/null 2>&1
if [ $? -eq 0 ]; then
    exit 0
else
    exit 1
fi
EOF'
```
Luego dale permisos de ejecución:
```text
sudo chmod 755 /etc/keepalived/check_nginx.sh
```

---

### 6. Configurar Keepalived en modo BACKUP
Abre el archivo de configuración:
```text
sudo nano /etc/keepalived/keepalived.conf
```
Borra lo que haya y pega esto dentro:
```text
vrrp_script check_nginx {
    script "/etc/keepalived/check_nginx.sh"
    interval 2
    weight 20
}

vrrp_instance VI_1 {
    state BACKUP
    interface enp0s3
    virtual_router_id 77
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass secret123
    }
    virtual_ipaddress {
        192.168.X.X
    }
    track_script {
        check_nginx
    }
}
```
*(Guarda con `Ctrl + O`, presiona `Enter` y sal con `Ctrl + X`)*.

---

### 7. Asegurar que el servicio SSH esté activo
Pon este comando:
```text
sudo systemctl enable --now ssh
```

---

### 8. Apagar la máquina
Pon este comando:
```text
sudo poweroff
```

---

### 9. Configuración final en VirtualBox
1. Ve a **Configuración** de la máquina virtual.
2. Ve a **Red** > **Adaptador 1**.
3. Cambia **Conectado a:** por **Adaptador Puente**.
4. En **Nombre**, elige tu tarjeta **Wi-Fi**.
5. Dale a **Aceptar**.
