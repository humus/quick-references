# Postgresql

## Build from source
```
prefix=$HOME/local
sudo apt-get install -y libreadline-dev libossp-uuid-dev
./configure --prefix=$HOME/local --with-uuid=ossp
make && make install

```

## Start using db server
```
datadir=$HOME/postgres_data
cd $prefix
bin/pg_ctl -D $datadir initdb # self-describing

# consider running: $ sed -i '/^host/s/trust/md5/' $datadir/pg_hba.conf
bin/pg_ctl -D $datadir start  # self-describing

# adding --echo let's you see the DDL commands
# may use -h and -U / --host={host} --username={username}
bin/createuser --pwprompt --createdb \
    --no-superuser pocdbadmin # will ask for password and confirmation

export PGPASSWORD={password} # don't ask for password if Env var exists
bin/createdb --owner pocdbadmin --echo --username pocdbadmin --host=localhost pocdb

#db is ready to accept connecions
psql -U pocdbadmin -d pocdb

# stop working with the db
bin/pg_ctl -D $datadir stop  # self-describing
```
