# Packer Templates For Making Provisioned Windows VMs

Note: Turn off updates in the answer files for faster build time

## SQL Server on Windows 2012 r2 Core

Unzip the files found in the installer at http://2014-dev-64bit.zip in the sql-server-install-files directory (there should already be a ConfigurationFile.ini in the directory, do not delete it)

### Build a Box

1. make base ovf

`packer build windows_2012_r2_core.json`

2. install SQL Server

`packer build install_sql_server.json`

3. compact and create database_server_core_virtualbox.box

`packer build compact_and_create_box.json`

4. add box to vagrant

`vagrant box add sql-server-core ./database_server_core_virtualbox.box`

### Setup Environment and Run the App Using a Local DB

1. initialize a new Vagrant environment for the local db

`vagrant init sql-server-core`

2. forword SQL Server's tcp port

add the following to the generated VagrantFile

`config.vm.network "forwarded_port", guest: 1433, host: 1433`
`config.vm.network "private_network", ip: "192.168.32.54"`

3. start the vm

`vagrant up`

## Windows Client with SQL Server Management Studio

Unzip the files found in the installer at http://sql-server-install-files.zip in the sql-server-install-files directory (there should already be a SSMS-ConfigurationFile.ini in the directory, do not delete it)

### Build a Windows 10 SSMS Box

1. make base ovf

`packer build windows_10.json`

2. install SSMS

`packer build install_ssms.json`

3. add box to vagrant

`vagrant box add ssms-windows-10 ssms_windows_10_virtualbox.box`

### Setup Environment and Connect SSMS to the Local DB

1. initialize a new Vagrant environment for SSMS

`vagrant init ssms-windows-10`

2. run vagrant up on sql-server-core and ssms-windows-10 vms

3. inside the ssms-windows-10 vm, connect to the db using SSMS

search for "ssms" and run it.
server name: 192.168.32.54
authentication: SQL (mixed mode)
name: sa
password: vagrant1!

## References:

<https://github.com/lostapathy/packer-templates>

<https://github.com/joefitzgerald/packer-windows>
