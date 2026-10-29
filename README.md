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


-------

```text
sudo nano /etc/keepalived/keepalived.conf
```

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
        10.240.226.200
    }
    track_script {
        check_nginx
    }
}
```


4.11111111111----------------
```text
& "C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" controlvm "SIS313-Lab4.1-Apps" keyboardputstring "sudo bash -c 'cat << ""EOF"" > /etc/netplan/50-cloud-init.yaml`nnetwork:`n  version: 2`n  ethernets:`n    enp0s3:`n      dhcp4: true`nEOF`nsudo netplan apply && ip a show enp0s3'`n"


```

```text
& "C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" controlvm "SIS313-Lab4.1-DB" keyboardputstring "sudo bash -c 'cat << ""EOF"" > /etc/netplan/50-cloud-init.yaml`nnetwork:`n  version: 2`n  ethernets:`n    enp0s3:`n      dhcp4: true`nEOF`nsudo netplan apply && ip a show enp0s3'`n"
```


---

19/10

```text
& "C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" controlvm "Lab4.1-Apps1" keyboardputstring "echo bmV0d29yazoKICB2ZXJzaW9uOiAyCiAgZXRoZXJuZXRzOgogICAgZW5wMHMzOgogICAgICBkaGNwNDogdHJ1ZQo= | base64 -d | sudo tee /etc/netplan/50-cloud-init.yaml && sudo netplan apply`n"
```

```text
& "C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" controlvm "Lab4.1-Apps2" keyboardputstring "echo bmV0d29yazoKICB2ZXJzaW9uOiAyCiAgZXRoZXJuZXRzOgogICAgZW5wMHMzOgogICAgICBkaGNwNDogdHJ1ZQo= | base64 -d | sudo tee /etc/netplan/50-cloud-init.yaml && sudo netplan apply`n"
```

```text
cat << 'EOF' > ~/apps/app1_3001/.env
PORT=3001
DB_HOST=172.20.10.7
DB_USER=usr_movies
DB_PASSWORD=secret
DB_NAME=db_movies
EOF
sed -i '/ssl: {/,/}/d' ~/apps/app1_3001/app.js
cd ~/apps/app1_3001 && pm2 restart all && pm2 save
```


```text
cat << 'EOF' > ~/apps/app2_3002/.env
PORT=3002
DB_HOST=172.20.10.7
DB_USER=usr_movies
DB_PASSWORD=secret
DB_NAME=db_movies
EOF
sed -i '/ssl: {/,/}/d' ~/apps/app2_3002/app.js
cd ~/apps/app2_3002 && pm2 restart all && pm2 save
```


```text
cd ~/apps/app2_3002 && pm2 start app.js --name app2_3002 && pm2 save --force
```

```text
cd ~/apps/app1_3001 && pm2 start app.js --name app1_3001 && pm2 save --force
```

---

Aquí tienes cada comando en su bloque listo para copiar y pegar, con su descripción corta abajo para tu informe:

---

### 📸 CAPTURA 1: Netplan y Dirección IP
```text
sudo cat /etc/netplan/50-cloud-init.yaml && ip a show enp0s8
```
✍️ **Texto para tu informe:**  
*"Configuración de interfaz interna enp0s8 con IP estática 192.168.10.2/29 y DNS local en Netplan."*

---

### 📸 CAPTURA 2: Validación de Zonas en BIND9
```text
sudo named-checkzone andescloud.local /etc/bind/db.andescloud.local && sudo named-checkzone 10.168.192.in-addr.arpa /etc/bind/db.192.168.10
```
✍️ **Texto para tu informe:**  
*"Validación de sintaxis correcta en los archivos de zona directa e inversa de BIND9."*

---

### 📸 CAPTURA 3: Resolución DNS Directa e Inversa
```text
dig @127.0.0.1 www.andescloud.local +short && dig @127.0.0.1 app.andescloud.local && dig -x 192.168.10.2 @127.0.0.1 +short
```
✍️ **Texto para tu informe:**  
*"Pruebas de resolución DNS directa (A y CNAME) e inversa (PTR) para el dominio andescloud.local."*

---

### 📸 CAPTURA 4: Estado de Procesos en PM2
```text
pm2 status
```
✍️ **Texto para tu informe:**  
*"Estado de las dos instancias de aplicación Node.js activas y gestionadas por PM2 en modo producción."*

---

### 📸 CAPTURA 5: Configuración de Nginx y Test de Sintaxis
```text
cat /etc/nginx/sites-available/andescloud.local && sudo nginx -t
```
✍️ **Texto para tu informe:**  
*"Configuración de Nginx como Proxy Inverso con algoritmo Least Connection y validación de sintaxis."*

---

### 📸 CAPTURA 6: Alternancia de Peticiones con curl
```text
for i in {1..4}; do curl -s http://www.andescloud.local | grep -E "Puerto|AndesCloud"; echo "---"; done
```
✍️ **Texto para tu informe:**  
*"Distribución equitativa de peticiones HTTP entre las instancias en los puertos 3001 y 3002 a través del balanceador."*

---

### 📸 CAPTURA 7: Prueba de Carga con Apache Bench
```text
ab -n 100 -c 10 http://www.andescloud.local/
```
✍️ **Texto para tu informe:**  
*"Prueba de estrés con Apache Bench demostrando alta disponibilidad y 0% de fallos bajo carga concurrente."*

---

### 📸 CAPTURA 8: Subinterfaces VLAN Activas
```text
ip -br a
```
✍️ **Texto para tu informe:**  
*"Segmentación lógica departamental mediante subinterfaces VLAN (10, 20 y 30) activas sobre enp0s8."*

---

### 📸 CAPTURA 9: Reglas Numeradas de UFW
```text
sudo ufw status numbered
```
✍️ **Texto para tu informe:**  
*"Lista numerada de reglas de firewall en UFW y control de tráfico de enrutamiento Inter-VLAN."*

---

### 📸 CAPTURA 10: Estado de Prometheus y Node Exporter
```text
sudo systemctl status prometheus prometheus-node-exporter --no-pager
```
✍️ **Texto para tu informe:**  
*"Servicios de Prometheus y Node Exporter activos y en ejecución en el servidor."*

---

### 📸 CAPTURA 11: Endpoints Activos (UP) en Prometheus
```text
curl -s http://localhost:9090/api/v1/targets | grep -o '"health":"[^"]*"'
```
✍️ **Texto para tu informe:**  
*"Comprobación de la API de Prometheus evidenciando que los endpoints de monitoreo se encuentran en estado UP."*  
*(Si tomaste la captura desde el navegador en `http://localhost:9090/targets`, puedes poner: "Panel de Targets en Prometheus evidenciando que los endpoints de monitoreo local y de VLANs se encuentran en estado UP").*
