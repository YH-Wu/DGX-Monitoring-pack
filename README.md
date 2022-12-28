**FIRST DEPLOYMENT**

It's suggest to put this folder to a external storage and mount this folder on every node so that every node could read same file.

**DEPLOYMENT FOR DCGM EXPORTER AND NODE EXPORTER**

Just launch start_exporter.sh in every compute node that you want to monitor.

**DEPLOYMENT FOR MONITORING SERVICES**

1. Fill IPs of every compute node in ./prometheus_provisioning/prometheus-config.yaml
2. Fill the folder path that you want to store your metric data and the desire db size and store time in ./.env., especially following environment variable:
   PROM_DATA_DIR=/path/to/store/promdata
   GRAFANA_DATA_DIR=/path/to/store/grafanadata
   PROMETHEUS_STORAGE_TSDB_RETENTION_TIME=365d
   PROMETHEUS_STORAGE_TSDB_RETENTION_SIZE=100GB
3. Launch start_monitoring.sh to create Prometheus and Grafana, ONLY need to do this step on ONE NODE, could be deploy without GPU environemnt but if you want you can stiil deploy these on GPU node as well.


**LATER ON, IF YOU NEED ADD MORE COMPUTE NODE FOR MONITORING**

1. Fill new IPs of every compute node in ./prometheus_provisioning/prometheus-config.yaml
2. Launch restart_monitoring.sh to restart Prometheus and Grafana service on the monitoring node.


**FOR DEBUG**

Stop monitoring service : ./stop_monitoring.sh
Stop DCGM exporter and node exporter : ./stop_exporter.sh


**TIPS**

In case of Promethus query wrong timestep data from exporter, it's suggest to wait systemd-timesyncd.service start.
$ sudo vim /etc/systemd/system/multi-user.target.wants/docker.service
Append "systemd timesyncd.service" in the end of After= line.

In case of Promethus not able to save data on external storage after reboot, it's suggest to wait mount point start.
$ systemctl list units type=mount

Find the desired file path of mount point and add it into docker service
$ sudo vim /etc/systemd/system/multi-user.target.wants/docker.service
Append "xxx.mount" in the end of After= line.
