- Install
- do not start `sphinxsearch` service after installation
```
$ sudo add-apt-repository ppa:builds/sphinxsearch-rel22
$ sudo apt update
$ sudo apt install sphinxsearch
$ sudo service sphinxsearch status
```

- Configure
```
$ cd /etc/sphinxsearch
$ sudo cp sphinx.conf sphinx.conf.org
$ sudo nano sphinx.conf

# and replace everything with

source src1
{
  type          = mysql

  sql_host      = localhost
  sql_user      = root
  sql_pass      = your_root_mysql_password
  sql_db        = test
  sql_port      = 3306

  sql_query     = \
  SELECT id, group_id, UNIX_TIMESTAMP(date_added) AS date_added, title, content \
  FROM documents

  sql_attr_uint         = group_id
  sql_attr_timestamp    = date_added
}
index test1
{
  source            = src1
  path              = /var/lib/sphinxsearch/data/test1
  docinfo           = extern
}
searchd
{
  listen            = 9306:mysql41
  log               = /var/log/sphinxsearch/searchd.log
  query_log         = /var/log/sphinxsearch/query.log
  read_timeout      = 5
  max_children      = 30
  pid_file          = /var/run/sphinxsearch/searchd.pid
  seamless_rotate   = 1
  preopen_indexes   = 1
  unlink_old        = 1
  binlog_path       = /var/lib/sphinxsearch/data
}

# run indexer
$ sudo indexer --all

# start server
$ sudo service sphinxsearch start

# to run indexer periodically, add to crontab
$ @hourly /usr/bin/indexer --rotate --config /etc/sphinxsearch/sphinx.conf --all
```

#### Autostart
- Sphinxsearch is turned off by default, to autostart
```
$ sudo sed -i 's/START=no/START=yes/g' /etc/default/sphinxsearch
```

#### Links
- https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-sphinx-on-ubuntu-16-04
