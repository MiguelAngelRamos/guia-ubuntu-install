### **1. Instalación de Node Exporter**
Node Exporter se usa para recolectar métricas del servidor, como el uso de CPU, memoria y disco.

1. **Descargar la última versión de Node Exporter:**

   ```bash
   cd /tmp
   curl -LO https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz
   ```

2. **Extraer el archivo:**

   ```bash
   tar xvf node_exporter-1.6.1.linux-amd64.tar.gz
   ```

3. **Mover Node Exporter al directorio adecuado:**

   ```bash
   sudo cp node_exporter-1.6.1.linux-amd64/node_exporter /usr/local/bin/
   ```

4. **Crear un usuario de sistema para Node Exporter:**

   ```bash
   sudo useradd -rs /bin/false node_exporter
   ```

5. **Configurar el servicio de Node Exporter:**

   Crea un archivo de servicio para Node Exporter:

   ```bash
   sudo nano /etc/systemd/system/node_exporter.service
   ```

   Añade lo siguiente:

   ```ini
   [Unit]
   Description=Node Exporter
   Wants=network-online.target
   After=network-online.target

   [Service]
   User=node_exporter
   Group=node_exporter
   Type=simple
   ExecStart=/usr/local/bin/node_exporter

   [Install]
   WantedBy=multi-user.target
   ```

6. **Iniciar y habilitar Node Exporter:**

   ```bash
   sudo systemctl daemon-reload
   sudo systemctl start node_exporter
   sudo systemctl enable node_exporter
   ```

7. **Verificar que Node Exporter esté funcionando:**

   Abre tu navegador y visita `http://<IP-de-tu-servidor>:9100/metrics`. Si todo está correcto, deberías ver las métricas del sistema.

---

### **2. Instalación de Prometheus**
Prometheus recolectará las métricas desde Node Exporter.

1. **Descargar la última versión de Prometheus:**

   ```bash
   cd /tmp
   curl -LO https://github.com/prometheus/prometheus/releases/download/v2.47.0/prometheus-2.47.0.linux-amd64.tar.gz
   ```

2. **Extraer el archivo:**

   ```bash
   tar xvf prometheus-2.47.0.linux-amd64.tar.gz
   ```

3. **Mover los binarios de Prometheus:**

   ```bash
   sudo cp prometheus-2.47.0.linux-amd64/prometheus /usr/local/bin/
   sudo cp prometheus-2.47.0.linux-amd64/promtool /usr/local/bin/
   ```

4. **Crear las carpetas de configuración y datos:**

   ```bash
   sudo mkdir /etc/prometheus
   sudo mkdir /var/lib/prometheus
   ```

5. **Copiar los archivos de configuración:**

   ```bash
   sudo cp prometheus-2.47.0.linux-amd64/prometheus.yml /etc/prometheus/
   sudo cp -r prometheus-2.47.0.linux-amd64/consoles /etc/prometheus/
   sudo cp -r prometheus-2.47.0.linux-amd64/console_libraries /etc/prometheus/
   ```

6. **Corregir los permisos de `/var/lib/prometheus`:**

   Prometheus necesita acceso al directorio `/var/lib/prometheus`:

   ```bash
   sudo chown -R prometheus:prometheus /var/lib/prometheus
   sudo chmod -R 775 /var/lib/prometheus
   ```

   Además, asegúrate de que el archivo de log de consultas esté disponible:

   ```bash
   sudo touch /var/lib/prometheus/query_log
   sudo chown prometheus:prometheus /var/lib/prometheus/query_log
   ```

7. **Crear un usuario para Prometheus:**

   ```bash
   sudo useradd -rs /bin/false prometheus
   ```

8. **Configurar el servicio de Prometheus:**

   Crea un archivo de servicio para Prometheus:

   ```bash
   sudo nano /etc/systemd/system/prometheus.service
   ```

   Añade lo siguiente:

   ```ini
   [Unit]
   Description=Prometheus
   Wants=network-online.target
   After=network-online.target

   [Service]
   User=prometheus
   Group=prometheus
   Type=simple
   ExecStart=/usr/local/bin/prometheus \
     --config.file=/etc/prometheus/prometheus.yml \
     --storage.tsdb.path=/var/lib/prometheus/ \
     --web.console.templates=/etc/prometheus/consoles \
     --web.console.libraries=/etc/prometheus/console_libraries

   [Install]
   WantedBy=multi-user.target
   ```

9. **Configurar la recolección de métricas desde Node Exporter:**

   Edita el archivo `/etc/prometheus/prometheus.yml` y añade la configuración para Node Exporter:

   ```yaml
   scrape_configs:
     - job_name: 'node_exporter'
       static_configs:
         - targets: ['localhost:9100']
   ```

10. **Iniciar y habilitar Prometheus:**

    ```bash
    sudo systemctl daemon-reload
    sudo systemctl start prometheus
    sudo systemctl enable prometheus
    ```

11. **Verificar que Prometheus esté funcionando:**

    Abre tu navegador y visita `http://<IP-de-tu-servidor>:9090`.

---

### **3. Instalación de Grafana**
Grafana se utiliza para visualizar las métricas recolectadas por Prometheus.

1. **Agregar la clave GPG de Grafana:**

   ```bash
   sudo apt-get install -y software-properties-common
   sudo wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
   ```

2. **Agregar el repositorio de Grafana:**

   ```bash
   sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
   sudo apt-get update
   ```

3. **Instalar Grafana:**

   ```bash
   sudo apt-get install grafana
   ```

4. **Iniciar y habilitar Grafana:**

   ```bash
   sudo systemctl start grafana-server
   sudo systemctl enable grafana-server
   ```

5. **Acceder a Grafana:**

   Abre tu navegador y visita `http://<IP-de-tu-servidor>:3000`. El usuario y contraseña predeterminados son `admin/admin`.

6. **Configurar Prometheus como fuente de datos en Grafana:**

   - Inicia sesión en Grafana.
   - Ve a **Configuración (engranaje)** > **Data Sources** > **Add data source**.
   - Selecciona **Prometheus**.
   - Introduce la URL: `http://localhost:9090`.
   - Haz clic en **Save & Test** para verificar la conexión.

---