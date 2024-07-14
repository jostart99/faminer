# faminer
Miner for [FACT0RN](https://www.fact0rn.io), based on yafu, cado-nfs, msieve

## Introduce
Mainly consists of a control center, a docker container, rpc server  
The control-center configure, control, and monitor the miner  
A docker container is running on the master  
The rpc server is processing communication between Master, Slave and report status to control-center  
Support CPU filtering and GPU filtering  
Linear algebra phase using GPU (tested on 3090 and 4090)

## How to start
### Precondition
A FACT0RN node is required, see [FACT0RN](https://github.com/FACT0RN/FACT0RN)
### Download
* [control-center.zip](https://github.com/jostart99/faminer/releases/download/latest/control-center.zip)
```
wget https://github.com/jostart99/faminer/releases/download/latest/control-center.zip
```
* [fact-rpc-server.deb](https://github.com/jostart99/faminer/releases/download/latest/fact-rpc-server.deb)
```
wget https://github.com/jostart99/faminer/releases/download/latest/fact-rpc-server.deb
```
### Install rpc server
You need to install rpc server on all the masters and slaves
```
apt install -f ./fact-rpc-server.deb
```
### Configure
You should use a computer to run the control-center
#### 1. Extract control-center
```
unzip control-center.zip
```
#### 2. Enter config folder, copy xxx.txt.example to xxx.txt
```
cd ~/fact/control-center/config
cp config.txt.example config.txt
cp group.txt.example group.txt
cp hostlist.txt.example hostlist.txt
```
#### 3. Edit hostlist.txt  
For convenience, the miner use aliases to communicate with each other, so you need to configure the IP and the corresponding alias, which can be the same or different from the host name, but can not be duplicated  
Example:
```
192.168.1.200 worker0
192.168.1.201 worker1
192.168.1.202 worker2
192.168.1.203 worker3
192.168.1.204 worker4
192.168.1.205 worker5
```
On the computer running the control center, copy the ip and alias to /etc/hosts
or run the script after editing hostlist.txt
```
cd ~/fact/control-center
sudo script/setup_script/set_etc_host.sh config/hostlist.txt
```
#### 4. Edit group.txt
The master and slave are separated by a comma, master at the beginning of the line, 
each line is a group  
Example (worker1 and worker11 are masters):
```
worker1,worker2,worker3,worker4,worker5
worker11,worker12,worker13,worker14,worker15
```
#### 5. Edit config.txt
You must fill the options: rpc_url, rpc_pass, rpc_user, script_key  
If you set up a docker registry, you can load and push the docker image to your own docker server and change the option docker_server
### Setup miner
Use control binary to setup miners
```
./control --setup all
```
or
```
./control --setup worker1,worker2,worker3,worker4,worker5
```
This will setup the miners: download required binaries, install drivers and apks, pull the docker container and so on
### Start miner
Use control binary to start miners
```
./control --start all
```
### Monitor
Run monitor binary to monitor the status of the mining machine from your terminal  
Before running monitor for the first time, you need to make sure that the python3 rich module is installed.
```
sudo apt install python3-pip
python3 -m pip install rich
```
then
```
./monitor
```
### Logs
You can find the logs you need in the /root/fact/logs directory of the master
