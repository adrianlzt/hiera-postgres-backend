## hiera-postgresql-backend

Alternate PostgreSQL backend for Hiera

This is a PostgreSQL backend for Hiera inspired by [hiera-mysql-backend](https://github.com/Telmo/hiera-mysql-backend). 


### What goes into the sql files.

The poorly named sql files are really yaml files where the key is the lookup key and the value is the SQL statement (it accepts interpolation).

Lets assume your _datadir_ is `/etc/puppet/hieradata/` and your hierarchy for hiera just have a common. hiera-postgresql-backend would look for /etc/puppet/hieradata/common.sql the common.sql would look like:

```yaml
---
applications: SELECT * FROM applications WHERE host='%{fqdn}';
coats: SELECT cut,name,type FROM coats WHERE color='brown';
default_query: SELECT dbhost, dbuser, dbpass FROM db_servers WHERE dbclient='%{fqdn}' and dbname='%{key}';
```

running `hiera applications` would run the query against the configured database.

The default_query key is special: it is in fact a default for anything in this hierarchy that is not specified with its own key. For example, running `hiera booksdb` would run default_query with the key variable in the query being interpolated as "booksdb".

Empty arrays and empty hashes returned from the database will be collapsed to nil as Puppet modules expect. Empty (zero-length) non-nil string values returned by the query suggest an actual empty column in the database and are returned as empty strings.

### Using

`gem install hiera-postgres-backend`


### Configuring Hiera

Hiera configuration is pretty simple

```yaml
---
:backends:
  - yaml
  - postgres

:yaml:
  :datadir: /etc/puppet/hieradata

:postgres:
  :datadir: /etc/puppet/hieradata
  :host: hostname
  :user: username
  :pass: password
  :database: database

:hierarchy:
  - "%{::clientcert}"
  - "%{::custom_location}"
  - common

:logger: console
```

## Known issues

1. This README is poorly written.


## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
