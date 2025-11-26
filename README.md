# CICD-End-2-End
sudo apt update
sudo apt install fontconfig openjdk-21-jre
java -version

sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt update
sudo apt install jenkins

sudo adduser --system --no-create-home --group sonarqube
sudo mkdir -p /opt/sonarqube
sudo chown -R sonarqube:sonarqube /opt/sonarqube

sudo mv /home/ubuntu/sonarqube-10.9.0.65366.zip /opt/
cd /opt
sudo unzip sonarqube-10.9.0.65366.zip
sudo mv sonarqube-10.9.0.65366 sonarqube
sudo chown -R sonarqube:sonarqube /opt/sonarqube

sudo nano /etc/security/limits.conf

sonarqube   -   nofile   131072
sonarqube   -   nproc    8192

sudo sysctl -w vm.max_map_count=524288
sudo sysctl -w fs.file-max=131072
ulimit -n 131072
ulimit -u 8192


sudo nano /etc/systemd/system/sonarqube.service

[Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=forking
User=sonarqube
Group=sonarqube
LimitNOFILE=131072
LimitNPROC=8192
ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop
Restart=on-failure

[Install]
WantedBy=multi-user.target

sudo systemctl daemon-reload
sudo systemctl enable sonarqube
sudo systemctl start sonarqube
sudo systemctl status sonarqube


