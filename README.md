# Getting started:
```
sudo easy_install pip
sudo pip install virtualenv
virtualenv env --python python2.7
. env/bin/activate
pip install -r requirements.txt
export DJANGO_SETTINGS_MODULE=openduty.settings_dev
python manage.py syncdb
python manage.py migrate
python manage.py collectstatic
python manage.py runserver
```
now, you can start hacking on it.

# After models you've changed your models please run:
```
./manage.py schemamigration openduty --auto
./manage.py schemamigration notification --auto
./manage.py migrate

```

# If you see a new file appearing in migrations directory when pulling from upstream please run
```
./manage.py migrate
```

# Celery worker:
```
celery -A openduty worker -l info
```

# Login using basic authentication with LDAP-backend

Add the following snippet to your settings_prod/dev.py, dont forget about import

```
AUTH_LDAP_SERVER_URI = "ldap://fqdn:389"
AUTH_LDAP_BIND_DN = ""
AUTH_LDAP_BIND_PASSWORD = ""
AUTH_LDAP_START_TLS = False
AUTH_LDAP_MIRROR_GROUPS = True #Mirror LDAP Groups as Django Groups, and populate them as well.
AUTH_LDAP_GROUP_SEARCH = LDAPSearch("ou=Group,dc=domain,dc=com",
    ldap.SCOPE_SUBTREE, "(&(objectClass=posixGroup)(cn=openduty*))"
)
AUTH_LDAP_GROUP_TYPE = PosixGroupType()

AUTH_LDAP_USER_SEARCH = LDAPSearch("ou=People,dc=domain,dc=com",
ldap.SCOPE_SUBTREE, "(uid=%(user)s)")

AUTH_LDAP_USER_ATTR_MAP = {
"first_name": "uid",
"last_name": "sn",
"email": "mail"
}


AUTHENTICATION_BACKENDS = (
    'django_auth_ldap.backend.LDAPBackend',
    'django.contrib.auth.backends.ModelBackend',
)

MIDDLEWARE_CLASSES = MIDDLEWARE_CLASSES + (
  'openduty.middleware.basicauthmiddleware.BasicAuthMiddleware',
)

```
