### Install Vagrant:
https://developer.hashicorp.com/vagrant/downloads


### Build Multiple Machines:
```bash
vagrant up
```

### Run these 3 manually on Slaves
- pg_basebackup -h 192.168.56.10 -U replica_user -X stream -v -R -W -D /var/lib/postgresql/14/main
- chown postgres -R /var/lib/postgresql/14/main/
- systemctl restart postgresql
> ssh to first slave: ```vagrant ssh slave1```

### Run this on Master to check replicas
- sudo -u postgres psql -c "SELECT client_addr, state FROM pg_stat_replication;"


### PS
- In this `Vagrantfile` we installed postgresql with `timescaledb`
- Timescaledb installation: https://docs.timescale.com/self-hosted/latest/install/installation-linux/
- You can replace the `md5` with `trust` in `pg_hba.conf` then you can run `pg_basebackup` with `-w` instead of `-W` and don't send the password

