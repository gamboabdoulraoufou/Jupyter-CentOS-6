### Install and configure JupyterHub on CentOS 6

> Step 0: Install Python 2.7 (Anaconda distribution) 

```sh
# Download Anaconda
wget https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda2-4.0.0-Linux-x86_64.sh

# Install Anacondo
bash Anaconda2-4.0.0-Linux-x86_64.sh

# Add anaconda folder to path
export PATH="/home/abdoulraouf_gambo/anaconda2/bin:$PATH"

```

> Step 1: Install Prerequisite Software

```sh
sudo yum install -y openssl-devel 
sudo yum install -y bzip2-devel 
sudo yum install -y expat-devel 
sudo yum install -y gdbm-devel 
sudo yum install -y readline-devel 
sudo yum install -y sqlite-devel
sudo yum install -y gcc

```

> step 2:  Download Python 3 from the Python Download Page

```sh
wget https://www.python.org/ftp/python/3.4.5/Python-3.4.5.tgz
tar -xvzf Python-3.4.5.tgz

```

> Step 3: Configure and Build

```sh
cd Python-3.4.5
sudo ./configure --prefix=/usr/local/python-3.4
make 
sudo make install

```

> Step 4: Check python 3 installation

```sh
# Go to python console
/usr/local/python-3.4/bin/python3
# Ctrl+D to exit python
```

> Step 5: Install pip 

```sh
cd
wget https://bootstrap.pypa.io/get-pip.py
sudo /usr/local/python-3.4/bin/python3 get-pip.py
```

> Step 6: Install Nodejs and npm and Javascript Dependencies

```sh
# 
sudo rpm -ivh http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm
sudo yum install -y npm
sudo yum install -y git
sudo npm install -g configurable-http-proxy

```

> Step 7: Install JupyerHub

```sh
sudo /usr/local/python-3.4/bin/python3 -m pip install jupyterhub
sudo /usr/local/python-3.4/bin/python3 -m pip install "ipython[notebook]"
sudo yum install -y python-dev 
sudo yum install -y python-setuptools
sudo yum install -y build-essential
sudo yum install -y py4j
sudo yum install -y python-pip
sudo pip install "ipython[notebook]"

```

> Step 8: Create jupyter configuration file

```sh
/usr/local/python-3.4/bin/python3 -m jupyterhub --generate-config -f /home/abdoulraouf_gambo/jupyterhub_config.py
```

> Step 9: Configure jupyter

```sh
# 1- Create and add SSL certificat, so that your password is not sent unencrypted by your browser
openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -nodes -days 365

# 2- Create cookie secret
openssl rand -base64 2048 > /home/abdoulraouf_gambo/cookie_secret
sudo chmod a-srwx /home/abdoulraouf_gambo/cookie_secret

# 3- Create auth token
openssl rand -hex 32 > /home/abdoulraouf_gambo/proxi_auth_token

# 4- Create auth token
sudo touch /var/log/jupyterhub.log

# 5- Change Jupyter configuration
# 5-1- Edit configuration file
sudo nano /home/abdoulraouf_gambo/jupyterhub_config.py

# 5-2- Add the content below in configugation file
c = get_config()
# IP and Port
c.JupyterHub.ip = '10.128.0.4' # IP local
c.JupyterHub.port = 9083
# Security - SSL
c.JupyterHub.ssl_key = '/home/abdoulraouf_gambo/key.pem'
c.JupyterHub.ssl_cert = '/home/abdoulraouf_gambo/cert.pem'
# Security - cookie secret
c.JupyterHub.cookie_secret_file ='/home/abdoulraouf_gambo/cookie_secret'
c.JupyterHub.db_url = '/home/abdoulraouf_gambo/jupyterhub.sqlite'
# Security - http token
c.JupyterHub.proxy_auth_token = '/home/abdoulraouf_gambo/proxi_auth_token'
# put the log file in /var/log
c.JupyterHub.extra_log_file = '/var/log/jupyterhub.log'
# specify users and admin
c.Authenticator.whitelist = {'abdoulraouf_gambo', 'abdoul', 'test'}
c.Authenticator.admin_users = {'abdoulraouf_gambo', 'abdoul'}
# Tell jupyter to use absolue path
c.Spawner.cmd = '/usr/local/python-3.4/bin/jupyterhub-singleuser'

```

> Step 10: Setup Spark kernel

```sh
# Create folder for Spark Kernel
sudo mkdir -p /usr/local/share/jupyter/kernels/pyspark/

# Create and add configuration information json file
cat <<EOF | sudo tee /usr/local/share/jupyter/kernels/pyspark/kernel.json
{
 "display_name": "PySpark",
 "language": "python",
 "argv": [
  "/home/abdoulraouf_gambo/anaconda2/bin/python",
  "-m",
  "ipykernel.kernelapp",
  "-f",
  "{connection_file}"
 ],
 "env": {
  "SPARK_HOME": "/usr/hdp/2.4.2.0-258/spark/",
  "PYTHONPATH": "/usr/hdp/2.4.2.0-258/spark/python/:/usr/hdp/2.4.2.0-258/spark/python/lib/py4j-0.9-src.zip",
  "PYTHONSTARTUP": "/usr/hdp/2.4.2.0-258/spark/python/pyspark/shell.py",
  "PYSPARK_SUBMIT_ARGS": "--num-executors 4 --executor-memory 3G --total-executor-cores 4 pyspark-shell",
  "PYSPARK_DRIVER_PYTHON":"/home/abdoulraouf_gambo/anaconda2/bin/python",
  "PYSPARK_PYTHON": "/home/abdoulraouf_gambo/anaconda2/bin/python"
 }
}
EOF


```

> Step 11: Setup Python 2 kernel

```sh
# Create folder for Python 2 Kernel
sudo mkdir -p /usr/local/share/jupyter/kernels/python2.7/

# Create and add configuration information json file
cat <<EOF | sudo tee /usr/local/share/jupyter/kernels/python2.7/kernel.json
{"display_name": "Python 2",
"language": "python",
"argv":
  ["/home/abdoulraouf_gambo/anaconda2/bin/python",
  "-c",
  "from ipykernel.kernelapp import main; main()",
  "-f",
  "{connection_file}" ]
}
EOF

```

> Step 12: Add execution right to other user

```sh
sudo chmod +x -R /home/abdoulraouf_gambo/
sudo chmod a-srwx /home/abdoulraouf_gambo/cookie_secret

```

> Step 13: Run Jupyter

```sh
# Launch Jupyter server
sudo /usr/local/python-3.4/bin/python3 -m jupyterhub -f /home/abdoulraouf_gambo/jupyterhub_config.py 

# Or
nohup sudo /usr/local/python-3.4/bin/python3 -m jupyterhub -f /home/abdoulraouf_gambo/jupyterhub_config.py &

```

> Step 14: Start all Ambari service on start up

```sh
export VISUAL=nano
crontab -e 
```
> Add the following code
```sh
# Run Jupyter on start up
@reboot nohup sudo /usr/local/python-3.4/bin/python3 -m jupyterhub -f /home/agambo/jupyterhub_config.py &
# Run Rstudio on start-up
@reboot sudo rstudio-server start
# Run some ambari service on start up
@rebootcurl -u admin:admin -i -H 'X-Requested-By: ambari' -X PUT -d '{"RequestInfo": {"context" :"Start HDFS via REST"}, "Body": {"ServiceInfo": {"state": "STARTED"}}}' http://localhost:8080/api/v1/clusters/hadoop/services/HDFS 
@rebootcurl -u admin:admin -i -H 'X-Requested-By: ambari' -X PUT -d '{"RequestInfo": {"context" :"Start YARN via REST"}, "Body": {"ServiceInfo": {"state": "STARTED"}}}' http://localhost:8080/api/v1/clusters/hadoop/services/YARN
@rebootcurl -u admin:admin -i -H 'X-Requested-By: ambari' -X PUT -d '{"RequestInfo": {"context" :"Start MAPREDUCE2 via REST"}, "Body": {"ServiceInfo": {"state": "STARTED"}}}' http://localhost:8080/api/v1/clusters/hadoop/services/MAPREDUCE2
@rebootcurl -u admin:admin -i -H 'X-Requested-By: ambari' -X PUT -d '{"RequestInfo": {"context" :"Start HIVE via REST"}, "Body": {"ServiceInfo": {"state": "STARTED"}}}' http://localhost:8080/api/v1/clusters/hadoop/services/HIVE
@rebootcurl -u admin:admin -i -H 'X-Requested-By: ambari' -X PUT -d '{"RequestInfo": {"context" :"Start TEZ via REST"}, "Body": {"ServiceInfo": {"state": "STARTED"}}}' http://localhost:8080/api/v1/clusters/hadoop/services/TEZ
@rebootcurl -u admin:admin -i -H 'X-Requested-By: ambari' -X PUT -d '{"RequestInfo": {"context" :"Start ZOOKEEPER via REST"}, "Body": {"ServiceInfo": {"state": "STARTED"}}}' http://localhost:8080/api/v1/clusters/hadoop/services/ZOOKEEPER
@reboot curl -u admin:admin -i -H 'X-Requested-By: ambari' -X PUT -d '{"RequestInfo": {"context" :"Start SPARK via REST"}, "Body": {"ServiceInfo": {"state": "STARTED"}}}' http://localhost:8080/api/v1/clusters/hadoop/services/SPARK
# Stop server after 00:01:00
1 0 * * * sudo shutdown -h 0
```

__Go to https://IP:9083 or https://your.host.com:9083__  

__Enjoy!__
