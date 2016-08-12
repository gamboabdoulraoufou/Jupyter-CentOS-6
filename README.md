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

git clone https://github.com/jupyter/jupyterhub.git
cd jupyterhub
/usr/local/python-3.4/bin/python3 -m pip install -r dev-requirements.txt -e .

Step 3b: Update Javascript
/usr/local/python-3.4/bin/python3 setup.py js
/usr/local/python-3.4/bin/python3 setup.py css

Step 4a: Update .bashrc or /etc/profile.d for python-3 path if you wish to affect global settings
export PATH=/usr/local/python-3.4/bin:$PATH

Step 4b: Launch the JupyterHub Server
jupyterhub

Step 5: Generate a default config file:
jupyterhub --generate-config


/usr/local/python-3.4.1/bin/pip3 install networkx







