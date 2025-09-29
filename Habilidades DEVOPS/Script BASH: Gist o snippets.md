### Script BASH

### Objetive:

Gists or snippets: small pieces of code or Bash scripts executed on Linux-Ubuntu, which I have applied to routine basic administration tasks on EC2 virtual machines with Docker containers.

##### revisar_sitio.sh
```
#Bash para verificar mi Sitio Profesional
#!/bin/bash
# Revisar URL de mi Sitio Profesional
URL="https://ocvpprofessional.cloud"

# Perform the HTTP request
HTTP_RESPONSE=$(curl -o /dev/null -s -w "%{http_code}" "$URL")

# Check if the HTTP response code is 200 (OK)
if [[ "$HTTP_RESPONSE" -eq 200 ]]; then
echo "Servicio Mi Sitio Prof esta activo  (HTTP $HTTP_RESPONSE)."
else
echo "Servicio MI Sitio Prof esta fallando! Received HTTP response: $HTTP_RESPONSE"
fi
```
##### paghtml_resultados.sh
```
# Script que genera HTML para mostrar resultados de monitoreo
#!/bin/bash
cat << EOF > /var/www/html/system-info.html
<!DOCTYPE html>
<html>
<head><title>Informacion de mi Ec2</title></head>
<body>
<h1>System Information</h1>
<pre>$(df -h)</pre>
<pre>$(free -h)</pre>
<pre>$(uptime)</pre>
</body>
</html>
EOF
```
##### backup_dir_wp.sh
```
# crea backup de mi carpeta worpress
#!/usr/bin/env bash
# Realiza un backup de un directorio a un archivo tar.gz
set -e
SOURCE_DIR=${1:-/home/wordpress}
BACKUP_DIR="~/home/wordpress/backups"
DATE=$(date +%Y%m%d-%H%M%S)
mkdir -p "$BACKUP_DIR"
BACKUP_FILE="${BACKUP_DIR}/backup-$(basename $SOURCE_DIR)-${DATE}.tar.gz"
echo "=== Creando backup de $SOURCE_DIR ==="
tar -czf "$BACKUP_FILE" "$SOURCE_DIR"
echo "✅ Backup creado: $BACKUP_FILE”
```


##### monitor_MyEC2.shmonitor_MyEC2.sh
```
 # BASH para monitorear mi EC2
#!/bin/bash 
THRESHOLD=80
# Check CPU load
CPU_LOAD=$(top -bn1 | grep "Cpu(s)" | awk '{print $2 + $4}')

echo "CPU Load: $CPU_LOAD%"
# Check memory usage
MEMORY=$(free | awk '/Mem:/ {printf "%.2f", $3/$2 * 100.0}')

echo "Memory Usage: $MEMORY%"
# Check disk space
DISK=$(df -h | awk '$NF=="/"{print $(NF-1)}' | sed 's/%//')

echo "Disk Usage: $DISK%"
# Alert if any metric exceeds threshold
if [[ "$CPU_LOAD" > "$THRESHOLD" || "$MEMORY" > "$THRESHOLD" || "$DISK" > "$THRESHOLD" ]]; then
echo "Alerta: esta métrica esta por encima de este nivel $THRESHOLD%"
fi
```