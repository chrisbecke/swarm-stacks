
service ?= mattermost
STACK = $(notdir $(PWD))
FILES += mattermost-stack.yml
SERVICE = $(STACK)_$(service)

# Mattermost Variables

# Domain of service
export DOMAIN ?= mm.example.com

# Container settings
## Timezone inside the containers. The value needs to be in the form 'Europe/Berlin'.
## A list of these tz database names can be looked up at Wikipedia
## https://en.wikipedia.org/wiki/List_of_tz_database_time_zones
export TZ = UTC

# Postgres settings
## Documentation for this image and available settings can be found on hub.docker.com
## https://hub.docker.com/_/postgres
## Please keep in mind this will create a superuser and it's recommended to use a less privileged
## user to connect to the database.
## A guide on how to change the database user to a nonsuperuser can be found in docs/creation-of-nonsuperuser.md
export POSTGRES_IMAGE_TAG = 13-alpine
#export POSTGRES_DATA_PATH = ./volumes/db/var/lib/postgresql/data

export POSTGRES_USER = mmuser
export POSTGRES_PASSWORD = mmuser_password
export POSTGRES_DB = mattermost

# Nginx
## The nginx container will use a configuration found at the NGINX_MATTERMOST_CONFIG. The config aims
## to be secure and uses a catch-all server vhost which will work out-of-the-box. For additional settings
## or changes ones can edit it or provide another config. Important note: inside the container, nginx sources
## every config file inside */etc/nginx/conf.d* ending with a *.conf* file extension.

## Inside the container the uid and gid is 101. The folder owner can be set with
## `sudo chown -R 101:101 ./nginx` if needed.
export NGINX_IMAGE_TAG = alpine

# Mattermost settings
## Inside the container the uid and gid is 2000. The folder owner can be set with
## `sudo chown -R 2000:2000 ./volumes/app/mattermost`.
# export MATTERMOST_CONFIG_PATH         = ./volumes/app/mattermost/config
# export MATTERMOST_DATA_PATH           = ./volumes/app/mattermost/data
# export MATTERMOST_LOGS_PATH           =./volumes/app/mattermost/logs
# export MATTERMOST_PLUGINS_PATH        =./volumes/app/mattermost/plugins
# export MATTERMOST_CLIENT_PLUGINS_PATH =./volumes/app/mattermost/client/plugins
# export MATTERMOST_BLEVE_INDEXES_PATH  =./volumes/app/mattermost/bleve-indexes

## Bleve index (inside the container)
export MM_BLEVESETTINGS_INDEXDIR = /mattermost/bleve-indexes

## This will be 'mattermost-enterprise-edition' or 'mattermost-team-edition' based on the version of Mattermost you're installing.
export MATTERMOST_IMAGE = mattermost-team-edition
export MATTERMOST_IMAGE_TAG = 7.1

## Make Mattermost container readonly. This interferes with the regeneration of root.html inside the container. Only use
## it if you know what you're doing.
## See https://github.com/mattermost/docker/issues/18
export MATTERMOST_CONTAINER_READONLY = false

## The app port is only relevant for using Mattermost without the nginx container as reverse proxy. This is not meant
## to be used with the internal HTTP server exposed but rather in case one wants to host several services on one host
## or for using it behind another existing reverse proxy.
export APP_PORT = 8065

## Configuration settings for Mattermost. Documentation on the variables and the settings itself can be found at
## https://docs.mattermost.com/administration/config-settings.html
## Keep in mind that variables set here will take precedence over the same setting in config.json. This includes
## the system console as well and settings set with env variables will be greyed out.

## Below one can find necessary settings to spin up the Mattermost container
export MM_SQLSETTINGS_DRIVERNAME = postgres
export MM_SQLSETTINGS_DATASOURCE = postgres://$(POSTGRES_USER):$(POSTGRES_PASSWORD)@postgres:5432/$(POSTGRES_DB)?sslmode=disable&connect_timeout=10

## Example settings (any additional setting added here also needs to be introduced in the docker-compose.yml)
export MM_SERVICESETTINGS_SITEURL = https://$(DOMAIN)

mattermost:
	docker stack deploy $(addprefix --compose-file ,$(FILES)) $(STACK)

ps:
	docker stack ps $(STACK)

logs:
	docker service logs $(SERVICE)

clean:
	docker stack rm $(STACK)