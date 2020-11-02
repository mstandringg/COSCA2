# -*- mode: ruby -*-
# vi: set ft=ruby :

class Hash
  def slice(*keep_keys)
    h = {}
    keep_keys.each { |key| h[key] = fetch(key) if has_key?(key) }
    h
  end unless Hash.method_defined?(:slice)
  def except(*less_keys)
    slice(*keys - less_keys)
  end unless Hash.method_defined?(:except)
end

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # Online Vagrantfile documentation is at https://docs.vagrantup.com.

  # The AWS provider does not actually need to use a Vagrant box file.
  config.vm.box = "dummy"

  config.vm.provider :aws do |aws, override|
    # We will gather the data for these three aws configuration
    # parameters from environment variables (more secure than
    # committing security credentials to your Vagrantfile).
    #
  
    aws.access_key_id = "ASIATOTJ35BVLXFPO5WC"
    aws.secret_access_key = "tGirZDKcmL60wIxuKCpUwDB6Hv20OUOpD5FuZHrd"
    aws.session_token = "FwoGZXIvYXdzEFMaDPciMmTUqNi8v5V2ByLLARqG1hMw2iNxemGekJefk+ecVKg2nnvRb5wtP0JloZM/EZg9sROXGrsOi++4aL52g/IvQaAdfS/uI68OnwFbPhxSvT3pJ66/gkfoIBnNVCDKAj+NIUh+Bi3bXyujFjA9oBoBl5DR7kTHBj/MWA8DGpSND2o5VoHgCt+jtFVj8242hm/8Su+31ZcGwBSScGhve0NXdudCj3882tzEhBdfGT2s/UKP9gf/klJMMXsF4JX31OQvbdtHNPLjKOTN2CWyEYwKEr08Q4GMN63FKNbL/fwFMi2ovZQs4c9IIB/ERUtzWE0KWVCmlJ8RCRWQltjUNZif3qjjYJyGTTgHPQoywpQ="

    # The region for Amazon Educate is fixed.
    aws.region = "us-east-1"

    # These options force synchronisation of files to the VM's
    # /vagrant directory using rsync, rather than using trying to use
    # SMB (which will not be available by default).
    override.nfs.functional = false
    override.vm.allowed_synced_folder_types = :rsync

    # Following the lab instructions should lead you to provide values
    # appropriate for your environment for the configuration variable
    # assignments preceded by double-hashes in the remainder of this
    # :aws configuration section.

    # The keypair_name parameter tells Amazon which public key to use.
    aws.keypair_name = "349"
    # The private_key_path is a file location in your macOS account
    # (e.g., ~/.ssh/something).
    override.ssh.private_key_path = "~/.ssh/349.pem"

    # Choose your Amazon EC2 instance type (t2.micro is cheap).
    aws.instance_type = "t2.micro"

    # You need to indicate the list of security groups your VM should
    # be in. Each security group will be of the form "sg-...", and
    # they should be comma-separated (if you use more than one) within
    # square brackets.
    #
    aws.security_groups = ["sg-0f1b94cc5bc04e9be"]

    # For Vagrant to deploy to EC2 for Amazon Educate accounts, it
    # seems that a specific availability_zone needs to be selected
    # (will be of the form "us-east-1a"). The subnet_id for that
    # availability_zone needs to be included, too (will be of the form
    # "subnet-...").
    aws.availability_zone = "us-east-1a"
    aws.subnet_id = "subnet-6342002e"

    # You need to chose the AMI (i.e., hard disk image) to use. This
    # will be of the form "ami-...".
    # 
    # If you want to use Ubuntu Linux, you can discover the official
    # Ubuntu AMIs: https://cloud-images.ubuntu.com/locator/ec2/
    #
    # You need to get the region correct, and the correct form of
    # configuration (probably amd64, hvm:ebs-ssd, hvm).
    #
    aws.ami = "ami-07a985bed28dfbc01"

    # If using Ubuntu, you probably also need to uncomment the line
    # below, so that Vagrant connects using username "ubuntu".
    override.ssh.username = "ubuntu"
  end

  config.vm.define "webserver" do |webserver|
   
    webserver.vm.hostname = "webserver"
    
    webserver.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2 php libapache2-mod-php php-mysql
            
           cp /vagrant/test-website.conf /etc/apache2/sites-available/
           a2ensite test-website
           a2dissite 000-default
           service apache2 reload
    SHELL
  end

config.vm.define "dbserver" do |dbserver|
    dbserver.vm.hostname = "dbserver"
    dbserver.vm.network "private_network", ip: "192.168.2.12"
    dbserver.vm.synced_folder ".", "/vagrant", owner: "vagrant", group: "vagrant", mount_options: ["dmode=775,fmode=777"]
    
    dbserver.vm.provision "shell", inline: <<-SHELL
      apt-get update
      export MYSQL_PWD='insecure_mysqlroot_pw'
      echo "mysql-server mysql-server/root_password password $MYSQL_PWD" | debconf-set-selections 
      echo "mysql-server mysql-server/root_password_again password $MYSQL_PWD" | debconf-set-selections

      # Install the MySQL database server.
      apt-get -y install mysql-server

      echo "CREATE DATABASE fvision;" | mysql

      echo "CREATE USER 'webuser'@'%' IDENTIFIED BY 'insecure_db_pw';" | mysql
      echo "GRANT ALL PRIVILEGES ON fvision.* TO 'webuser'@'%'" | mysql
      
      export MYSQL_PWD='insecure_db_pw'

      cat /vagrant/setup-database.sql | mysql -u webuser fvision

           sed -i'' -e '/bind-address/s/127.0.0.1/0.0.0.0/' /etc/mysql/mysql.conf.d/mysqld.cnf
      service mysql restart
    SHELL
  end




config.vm.define "convert" do |convert|
    convert.vm.hostname = "convert"
        convert.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2 php libapache2-mod-php php-mysql
            
            cp /vagrant/conversion-website.conf /etc/apache2/sites-available/
     
      a2ensite conversion-website
           a2dissite 000-default
           service apache2 reload
    SHELL
  end


end
