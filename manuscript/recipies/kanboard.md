# Kanboard

Kanboard is a Kanban tool, developed by [Frédéric Guillot](https://github.com/fguillot). (Who also happens to be the developer of my favorite RSS reader, [Miniflux](/recipies/miniflux/))

Features include:

* Visualize your work
* Limit your work in progress to be more efficient
* Customize your boards according to your business activities
* Multiple projects with the ability to drag and drop tasks
* Reports and analytics
* Fast and simple to use
* Access from anywhere with a modern browser
* Plugins and integrations with external services
* Free, open source and self-hosted
* Super simple installation

![](/images/kanboard.png)

## Ingredients

1. [Docker swarm cluster](/ha-docker-swarm/) with [persistent shared storage](/ha-docker-swarm/shared-storage-ceph.md)
2. [Traefik](/ha-docker-swarm/traefik) configured per design

## Preparation

### Setup data locations

Create the location for the bind-mount of the application data, so that it's persistent:

```
mkdir -p /var/data/kanboard
```


### Setup Docker Swarm

Create a docker swarm config file in docker-compose syntax (v3), something like this:

```
version: '3'

services:
  kanboard:
    image: kanboard/kanboard
    volumes:
     - /var/data/kanboard/data:/var/www/app/data
     - /var/data/kanboard/plugins:/var/www/app/plugins
    networks:
    - traefik
    deploy:
      labels:
        - traefik.frontend.rule=Host:kanboard.example.com
        - traefik.docker.network=traefik
        - traefik.port=80

networks:
  traefik:
    external: true
```


## Serving

### Launch Kanboard stack

Launch the Kanboard stack by running ```docker stack deploy kanboard -c <path -to-docker-compose.yml>```

Log into your new instance at https://**YOUR-FQDN**. Default credentials are admin/admin, after which you can change (under 'profile') and add more users.

## Chef's Notes

1. The default theme can be significantly improved by applying the [ThemePlus](https://github.com/phsteffen/kanboard-themeplus) plugin.
2. Kanboard becomes more useful when you integrate in/outbound email with [MailGun](https://github.com/kanboard/plugin-mailgun), [SendGrid](https://github.com/kanboard/plugin-sendgrid), or [Postmark](https://github.com/kanboard/plugin-postmark).