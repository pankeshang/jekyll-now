---
layout: post
title:  "Migrate Database From SQLite To PostgreSQL"
date:   2017-07-26 00:00:00 +0800
category: programming
tags:
  - python
  - django
  - postgresql
---

Task

I have a djagno project which has been developed on sqlite database on my local machine (it's easy and fast!)

We need to move it to production environment, which is running on PostgreSql.


Step 1. dump the data from sqlite db

used `python manage.py dumpdata --indent=2 > ~/dump.json` to dump the data out


Step 2. Get content type data in SQL format

we use python to generate the SQL components
```
from django.contrib.contenttypes.models import ContentType

content_type_data = []
for content_type in ContentType.objects.all():
    row = "({i.id}, '{i.app_label}', '{i.model}')".format(i=content_type)
    content_type_data.append(row)

```

so here we have the content types
```
print(',\n'.join(content_type_data))

(1, 'admin', 'logentry'),
(3, 'auth', 'group'),
(4, 'auth', 'permission'),
(5, 'contenttypes', 'contenttype'),
...
```



```
from django.contrib.auth.models import Permission
permission_data = []
for permission in Permission.objects.all():
    single_data = "{i.id}, '{i.name}', '{i.content_type_id}', '{i.codename}'".format(i=permission)
    permission_data.append(single_data)

```
here wre have the permissions

```
print(',\n'.join(permission_data))
(1, 'Can add log entry', 1, 'add_logentry'),
(2, 'Can change log entry', 1, 'change_logentry'),
(3, 'Can delete log entry', 1, 'delete_logentry'),
(7, 'Can add group', 3, 'add_group'),
...
```


Step3. create the PostgreSQL db using django admin

```
python manage migrate
```

noted that this would create the content type as well as permission by default. we need to override those from PSQL



```
BEGIN;

SET CONSTRAINTS ALL DEFERRED;


DELETE FROM django_content_type;


INSERT INTO django_content_type (id, app_label, model) VALUES
(1,  'admin',  'logentry'),
(3,  'auth',  'group'),
(4,  'auth',  'permission'),
(5,  'contenttypes',  'contenttype'),
...


DELETE FROM auth_permission;

INSERT INTO auth_permission (id, name, content_type_id, codename) VALUES
(1, 'Can add log entry', 1, 'add_logentry'),
(2, 'Can change log entry', 1, 'change_logentry'),
(3, 'Can delete log entry', 1, 'delete_logentry'),
...

```


Step4. need to update the sequences for content type and permission

```

truuue_commercial_db=# alter sequence django_content_type_id_seq restart with 64;
ALTER SEQUENCE

truuue_commercial_db=# select max(id) from auth_permission;
 max
-----
 189
(1 row)

truuue_commercial_db=# alter sequence auth_permission_id_seq restart with 190;
ALTER SEQUENCE

```

Step5. load the rest of the data into db using django manager

```
python manage.py loaddata < dump.json
```