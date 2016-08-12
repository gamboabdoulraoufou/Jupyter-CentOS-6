# Jupyter-CentOS-6

# Step 1: 
## Repository of CentOS 6 and Scientific Linux 6
### EPEL (Extra Packages for Enterprise Linux)
wget http://ftp.riken.jp/Linux/fedora/epel/6/x86_64/epel-release-6-8.noarch.rpm
wget http://ftp.riken.jp/Linux/fedora/epel/6/i386/epel-release-6-8.noarch.rpm 

### RPMForge (Repoforge)
wget http://packages.sw.be/rpmforge-release/rpmforge-release-0.5.2-2.el6.rf.x86_64.rpm
wget http://packages.sw.be/rpmforge-release/rpmforge-release-0.5.2-2.el6.rf.i686.rpm

### PUIAS (Princeton University Institute of Advanced Study) 
wget http://puias.math.ias.edu/data/puias/6/x86_64/os/Packages/puias-core-6-1.puias6.6.noarch.rpm


# Step 2: Install Prerequisite Software
yum install openssl-devel bzip2-devel expat-devel gdbm-devel readline-devel sqlite-devel

# step 3:  Download Python-3.4.1 from the Python Download Page
wget https://www.python.org/ftp/python/3.4.5/Python-3.4.5.tgz
tar -xvzf Python-3.4.5.tgz



# Step 4: Configure and Build
cd Python-3.4.5
./configure --prefix=/usr/local/python-3.4
make 
sudo make install

# Step 5: Check that scripts query the correct interpreter
/usr/local/python-3.4/bin/python3

Step 4a: Update .bashrc or /etc/profile.d for python-3 path if you wish to affect global settings
export PATH=/usr/local/python-3.4/bin:$PATH

# Step 6: Run setup.py from the Installation Directory of Python
/usr/local/python-3.4/bin/python3 setup.py install

# Install pip 
wget https://bootstrap.pypa.io/get-pip.py
/usr/local/python-3.4/bin/python3 get-pip.py

# Step 7: Install Python Modules (whatever you need. Here is an example)
You can use pip install to install packages using pip3. See Using pip to install python packages

# Step 8: Install Nodejs and npm and Javascript Dependencies. You will need to install and enable EPEL repository
rpm -ivh http://epel.mirror.net.in/epel/6/i386/epel-release-6-8.noarch.rpm
yum install npm
yum install git
npm install -g configurable-http-proxy


### Step 3a: Installation of JupyerHub
/usr/local/python-3.4/bin/python3 -m pip install "ipython[notebook]"
/usr/local/python-3.4/bin/python3 -m pip install jupyterhub
yum -y install python-dev python-setuptools
yum install python-pip python-dev build-essential
pip install py4j
pip install "ipython[notebook]"

/home/abdoulraouf_gambo/jupyterhub
/home/abdoulraouf_gambo/jupyterhub/build/scripts-3.4/jupyterhub
/home/abdoulraouf_gambo/jupyterhub/build/lib/jupyterhub
/home/abdoulraouf_gambo/jupyterhub/jupyterhub
/home/abdoulraouf_gambo/jupyterhub/scripts/jupyterhub
/usr/local/python-3.4/bin/jupyterhub
/usr/local/python-3.4/lib/python3.4/site-packages/jupyterhub

python3 -m jupyterhub --generate-config -f /home/abdoulraouf_gambo/jupyterhub_config.py


##### 3-2- Create and add SSL certificat, so that your password is not sent unencrypted by your browser
```sh
openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -nodes -days 365

```

##### 3-3- Create cookie secret
```sh
openssl rand -base64 2048 > /home/abdoulraouf_gambo/cookie_secret
sudo chmod a-srwx /home/abdoulraouf_gambo/cookie_secret

```

##### 3-4- Create auth token
```sh
openssl rand -hex 32 > /home/abdoulraouf_gambo/proxi_auth_token

```

##### 3-5- Create auth token
```sh
sudo touch /var/log/jupyterhub.log

```

##### 3-6- Change Jupyter configuration
```sh
# Edit configuration file
/home/abdoulraouf_gambo/jupyterhub_config.py

# Add the content below in configugation file
c = get_config()
# IP and Port
c.JupyterHub.ip = '10.128.0.4' # IP local
c.JupyterHub.port = 443
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
c.Authenticator.whitelist = {'abdoulraouf_gambo'}
c.Authenticator.admin_users = {'abdoulraouf_gambo'}

```

#### 4- Setup Spark kernel
```sh
# Create folder for Spark Kernel
sudo mkdir -p /usr/local/share/jupyter/kernels/pyspark/

# Create and add configuration information json file
cat <<EOF | sudo tee /usr/local/share/jupyter/kernels/pyspark/kernel.json
{
 "display_name": "PySpark",
 "language": "python",
 "argv": [
  "/usr/bin/python",
  "-m",
  "IPython.kernel",
  "-f",
  "{connection_file}"
 ],
 "env": {
  "SPARK_HOME": "/usr/hdp/2.4.2.0-258/etc/spark/",
  "PYTHONPATH": "/usr/hdp/2.4.2.0-258/spark/python/:/usr/hdp/2.4.2.0-258/spark/python/lib/py4j-0.9-src.zip",
  "PYTHONSTARTUP": "/usr/hdp/2.4.2.0-258/spark/python/pyspark/shell.py",
  "PYSPARK_SUBMIT_ARGS": "--master spark://hadoop-m:7077 --num-executors 2 --executor-memory 4G --total-executor-cores 2 pyspark-shell"
 }
}
EOF

```

#### 5- Setup Python 2.7 kernel
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


#### 7- Run Jupyter
```sh
# Launch Jupyter server
sudo /usr/local/python-3.4/bin/python3 -m jupyterhub -f /home/abdoulraouf_gambo/jupyterhub_config.py

# Or
nohup sudo /usr/local/python-3.4/bin/python3 -m jupyterhub -f /home/abdoulraouf_gambo/jupyterhub_config.py &
```

__Go to https://IP or your.host.com and enjoy!__







