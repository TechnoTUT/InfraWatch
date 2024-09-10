# InfraWatch
Grafana, Prometheus, SNMP Exporterによるインフラ監視を行います.  
Docker/Podman, Kubernetesに対応しています. 

## Installation
### Local
```bash
cat > /etc/yum.repos.d/prometheus.repo <<'EOF'
[prometheus]
name=Prometheus
baseurl=https://packagecloud.io/prometheus-rpm/release/el/$releasever/$basearch
repo_gpgcheck=1
enabled=1
gpgcheck=1
gpgkey=https://packagecloud.io/prometheus-rpm/release/gpgkey
sslverify=1
metadata_expire=300
EOF

dnf update -y
dnf install -y net-snmp net-snmp-utils net-snmp-libs net-snmp-devel prometheus grafana git
dnf install -y snmp_exporter-0.22.0-1.el9.x86_64
systemctl enable --now prometheus snmp_exporter grafana-server
firewall-cmd --add-port=3000/tcp --permanent
firewall-cmd --reload
git clone https://github.com/TechnoTUT/InfraWatch.git
cp -f ./InfraWatch/snmp.yml /etc/prometheus/snmp.yml
cp -f ./InfraWatch/prometheus.yml /etc/prometheus/prometheus.yml
systemctl restart prometheus snmp_exporter
```
### Docker
```bash
git clone https://github.com/TechnoTUT/InfraWatch.git
cd InfraWatch
docker-compose up -d
```
### Podman
RHEL系のOSの場合は, Podmanが便利です.  
```bash
# Podmanのインストール
sudo dnf install -y podman podman-plugins python3-pip
pip install podman-compose
```
```bash
git clone https://github.com/TechnoTUT/InfraWatch.git
cd InfraWatch
podman-compose up -d
```
### Kubernetes
設定を変更する場合は, `kube/prometheus-configmap.yml`を編集します.
```bash
git clone https://github.com/TechnoTUT/InfraWatch.git
cd InfraWatch
kubectl apply -f kube/
```