$install_postgresql = <<-POSTGRESQL 
sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add -
apt update
apt install -y postgresql-14
POSTGRESQL

$install_timescaledb = <<-TIMESCALEDB
echo "deb https://packagecloud.io/timescale/timescaledb/ubuntu/ $(lsb_release -c -s) main" | sudo tee /etc/apt/sources.list.d/timescaledb.list
wget --quiet -O - https://packagecloud.io/timescale/timescaledb/gpgkey | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/timescaledb.gpg
apt update
apt install timescaledb-2-postgresql-14 -y
TIMESCALEDB

$master_script = <<-MASTER_SCRIPT
sudo -u postgres psql -c "CREATE ROLE replica_user WITH REPLICATION LOGIN PASSWORD 'password';"
echo "listen_addresses = '192.168.56.10'" >> /etc/postgresql/14/main/postgresql.conf
echo "wal_level = replica" >> /etc/postgresql/14/main/postgresql.conf
echo "wal_log_hints = on" >> /etc/postgresql/14/main/postgresql.conf
echo "host    replication     replica_user    192.168.56.11/24         md5" >> /etc/postgresql/14/main/pg_hba.conf
echo "host    replication     replica_user    192.168.56.12/24         md5" >> /etc/postgresql/14/main/pg_hba.conf
systemctl restart postgresql
MASTER_SCRIPT

$slave_script = <<-SLAVE_SCRIPT
systemctl stop postgresql
rm -rv /var/lib/postgresql/14/main/
SLAVE_SCRIPT

Vagrant.configure("2") do |config|

  config.vm.provision "shell", inline: $install_postgresql
  config.vm.provision "shell", inline: $install_timescaledb
  
  config.vm.define "master" do |instance|
    instance.vm.box = "ubuntu/bionic64"
    instance.vm.network :private_network, ip: "192.168.56.10"
    instance.vm.provision "shell", inline: $master_script
  end

  config.vm.define "slave1" do |instance|
    instance.vm.box = "ubuntu/bionic64"
    instance.vm.network :private_network, ip: "192.168.56.11"
    instance.vm.provision "shell", inline: $slave_script
  end

  config.vm.define "slave2" do |instance|
    instance.vm.box = "ubuntu/bionic64"
    instance.vm.network :private_network, ip: "192.168.56.12"
    instance.vm.provision "shell", inline: $slave_script
  end
  
end
