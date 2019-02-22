## Setting up and Linking Accounts to Audit
=====================

### Configure the Application

Security Monkey ships with a config for this quickstart guide called config.py. You can override this behavior by setting the `SECURITY_MONKEY_SETTINGS` environment variable.

Modify `env-config/config.py`:
- `FQDN`: Add the IP or DNS entry of your instance.
- `SQLALCHEMY_DATABASE_URI`: This config assumes that you are using the local db option. If you setup AWS RDS or GCP Cloud SQL as your database, you will need to modify the SQLALCHEMY_DATABASE_URI to point to your DB.
- `SECRET_KEY`: Something random.
- `SECURITY_PASSWORD_SALT`: Something random.

For an explanation of the configuration options, see [options](../options.md).

### Create the database tables:

Security Monkey uses Flask-Migrate (Alembic) to keep database tables up to date. To create the tables, run this command:

    cd /usr/local/src/security_monkey/
    monkey db upgrade

### Add Your AWS/GCP Accounts

You'll need to add at least one account before starting the scheduler. It's easiest to add them from the command line, but it can also be done through the web UI. :

    monkey add_account_aws
    usage: monkey add_account_aws [-h] -n NAME [--thirdparty] [--active]
                                  [--notes NOTES] --id IDENTIFIER
                                  [--update-existing]
                                  [--canonical_id CANONICAL_ID]
                                  [--s3_name S3_NAME] [--role_name ROLE_NAME]

    monkey add_account_gcp
    usage: monkey add_account_gcp [-h] -n NAME [--thirdparty] [--active]
                                  [--notes NOTES] --id IDENTIFIER
                                  [--update-existing] [--creds_file CREDS_FILE]

    monkey add_account_openstack
    usage: monkey add_account_openstack [-h] -n NAME [--thirdparty] [--active]
                                  [--notes NOTES] --id IDENTIFIER
                                  [--update-existing]
                                  [--cloudsyaml_file CLOUDSYAML_FILE]

For clarity: the `-n NAME` refers to the name that you want Security Monkey to use to associate with the account.
A common example would be "test" for your testing AWS account or "prod" for your main production AWS account. These names are unique.
Note that `--role_name` defaults to "SecurityMonkey", if you are using a different role, this value is just the role name, not the ARN.

The `--id IDENTIFIER` is the back-end cloud service identifier for a given provider. For AWS, it's the 12 digit account number, 
and for GCP, it's the project ID. For OpenStack, it's the cloud configuration to load from the clouds.yaml file.

## Setup Nginx for Security Monkey:
=====================

Security Monkey uses gunicorn to serve up content on its internal 127.0.0.1 address. For better performance, and to offload the work of serving static files, we wrap gunicorn with nginx. Nginx listens on 0.0.0.0 and proxies some connections to gunicorn for processing and serves up static files quickly.

### securitymonkey.conf

Copy the config file into place:

    sudo cp /usr/local/src/security_monkey/nginx/security_monkey.conf /etc/nginx/sites-available/security_monkey.conf
    sudo ln -s /etc/nginx/sites-available/security_monkey.conf /etc/nginx/sites-enabled/security_monkey.conf
    sudo rm /etc/nginx/sites-enabled/default
    sudo service nginx restart
    
### Start the API server

Manually start Security Monkey with `monkey run_api_server`. For autostarting your security monkey instance follow the [Netflix Documentation](https://github.com/Netflix/security_monkey/blob/develop/docs/autostarting.md)

### Manually Loading Data into Security Monkey
--------------------------------
To initially get data into Security Monkey, you can run the `monkey find_changes` command. This will go through
all your configured accounts in Security Monkey, fetch details about the accounts, store them into the database,
and then audit the items for any issues. 

The `find_changes` command can be further scoped to account and technology with the `-a account` and `-m technology` parameters.