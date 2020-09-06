# Gestión de infraestructuras virtuales

**Ejercicio 1**

>Instalar una máquina virtual Debian usando Vagrant y conectar con ella.

	Vagrant.configure("2") do |config|

	    # Ahora tenemos que indicar que box queremos usar
	    # Esto es el SO base que queremos que tenga nuestra máquina virtual
	    config.vm.box = "debian/buster64"

	    config.vm.provider "virtualbox" do |virtualbox|
	        virtualbox.memory = 2048
	        virtualbox.cpus = 2
	    end
	end

Ejecutamos ```vagrant up``` para comenzar la máquina virtual y después ```ǜagrant ssh``` para conectarnos a ella.

**Ejercicio 2**

>Instalar una máquina virtual ArchLinux o FreeBSD para KVM, otro hipervisor libre, usando Vagrant y conectar con ella.

	vagrant init archlinux/archlinux
	vagrant up
	vagrant ssh

**Ejercicio 3**

>Crear un script para provisionar de forma básica una máquina
virtual para el proyecto que se esté llevando a cabo en la asignatura.

	script_path = './scripts'

	Vagrant.configure("2") do |config|
	  config.vm.box = "ubuntu/xenial64"
	  config.vm.network "forwarded_port", guest: 80, host: 8080

	  config.vm.provision "shell" do |script|
	    script.path = "#{script_path}/script.sh"
	  end
	end

Ejecutamos los siguientes comandos:

	vagrant up
	vagrant provision

**Ejercicio 4**

>Configurar tu máquina virtual usando `vagrant` con el provisionador
ansible.

**workstate.yml**

	- hosts: all

	  # Activar la escalada de privilegios
	  become: yes

	  roles:
	    - enix.mongodb

	  tasks:

	    # Servicio base de datos mongodb
	    # Iniciará la base de datos en caso de que no lo estuviera
	    # gracias al parámetro state con valor started
	    - name: MongoDB
	      service:
	        name: mongod
	        state: started
			
**Vagrantfile**

	Vagrant.configure("2") do |config|
	    config.vm.box = "ubuntu/bionic64"

	    # Configuramos que puertos del host van a acceder a la máquina virtual
	    config.vm.network "forwarded_port", guest: 8000, host: 8000
	    config.vm.network "forwarded_port", guest: 8080, host: 8080

	    # Aprovisionamos la máquina virtual con Ansible
	    config.vm.provision "ansible" do |ansible|
	        ansible.inventory_path = "./ansible_hosts"
	        ansible.limit="vagrantlocal"
	        ansible.playbook = "./workstate.yml"
	end
