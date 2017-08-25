Vagrant.configure(2)  do |config|
  config.vm.box = "ubuntu/trusty64"

  config.vm.define "graylog" do |gl|
    gl.vm.hostname = "graylog"
    gl.vm.network :forwarded_port, guest: 80, host: 8080
    gl.vm.network :forwarded_port, guest: 12201, host: 12201, protocol: 'udp'
    gl.vm.network :forwarded_port, guest: 12201, host: 12201, protocol: 'tcp'
    
    gl.vm.provider :virtualbox do |host|
      host.memory = 8192
      host.cpus = 4
    end
  end

  # ES datanode
  config.vm.define "elasticsearch1" do |el1|
    el1.vm.hostname = "elasticnode1"
  end

  $script = <<SCRIPT
    # install Oracle Java 8
    apt-add-repository -y ppa:webupd8team/java
    
    # Pre-accept the oracle java software license so we are not promted.
    echo debconf shared/accepted-oracle-license-v1-1 select true | sudo debconf-set-selections
    echo debconf shared/accepted-oracle-license-v1-1 seen true | sudo debconf-set-selections

    # Get Logstash 
    wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
    echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list
   
    # Update apt
    apt-get update

    # Install Oracle Java 8.  Only output errors.
    apt-get -yqq install oracle-java8-installer

    echo 'Going to download Graylog...'
    #curl -S -s -L -O https://packages.graylog2.org/releases/graylog-omnibus/ubuntu/graylog_latest.deb
    dpkg -i /vagrant/graylog_latest.deb
    graylog-ctl set-external-ip http://127.0.0.1:8080/api
    graylog-ctl reconfigure

    # Install logstash
    #dpkg -i /vagrant/logstash-5.5.2.deb
    apt-get install logstash
SCRIPT

  config.vm.provision "shell", inline: $script
end
