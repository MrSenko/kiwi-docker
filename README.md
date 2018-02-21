Docker compose for Kiwi TCMS
============================

This is a docker compose to help you install and configure the
[Kiwi TCMS](http://kiwitcms.org) test case management
system when running as a Docker container. The existing configuration
is a downstream distribution designed and supported by
[Mr. Senko, Ltd.](http://mrsenko.com) and thus differs from the upstream
docker-compose and Docker image for Kiwi TCMS!


Create Docker container
-----------------------

Before you start you will need the `mrsenko/kiwi:<version>` image pulled
locally. Alternatively you have to build it yourself!

You can then start using Kiwi TCMS by executing:

    docker-compose up -d

This will create two containers:

1) A web container based on the latest Kiwi TCMS image
2) A DB container based on the official centos/mariadb image


`docker-compose` will also create two volumes for persistent data storage:
`kiwi_db_data` and `kiwi_uploads`.


Initial configuration of running container
------------------------------------------

You need to do initial configuration by executing:

    docker exec -it kiwi_web /Kiwi/manage.py migrate
    docker exec -it kiwi_web /Kiwi/manage.py createsuperuser


Upgrading
---------

To upgrade running Kiwi TCMS containers execute the following commands::

    docker-compose down
    docker pull mrsenko/kiwi # to fetch latest version from Docker.io
    docker-compose up -d
    docker exec -it kiwi_web /Kiwi/manage.py migrate

**NOTE:**

    Uploads and database data should stay intact because they are split into
    separate volumes, which makes upgrading very easy. However you may want to
    back these up before upgrading!


SSL configuration
-----------------

By default Kiwi TCMS is served via HTTPS. This docker compose is configured with
a default self-signed certificate. If you want to use a different SSL certificate
you need to update the `kiwi-https.key` and `kiwi-https.crt` files! More information
about generating your own self-signed certificates can be found at
https://wiki.centos.org/HowTos/Https.


Customization
-------------

The local directory `requirements.d` may contain text files listing additional
pip packages. These will be installed when rebuilding the docker image. To avoid
git conflicts you should place extra packages in a separate file with the `.txt`
extension!

The local file `product.py` is mounted inside the running Docker container.
You can add any site-specific settings to this file.

**IMPORTANT**

    Be sure to provide correct values for the `SECRET_KEY` and `ADMINS`
    settings!


**WARNING**

    Be careful not to upload your local `product.py` to GitHub!

You can also build your own customized version of Kiwi TCMS by adjusting
the contents of `Dockerfile` or inheriting from the base Docker images and then:

    docker build -t my_org/my_kiwi:<version> .

Next make sure to modify `docker-compose.yml` to use your customized image
instead the default `mrsenko/kiwi:latest`!
