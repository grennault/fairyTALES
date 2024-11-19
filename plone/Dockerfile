# NOTE: LATEST VULNERABLE PLONE VERSION. DO NOT UPGRADE.
ARG PLONE_VERSION=6.0.6
FROM plone/server-builder:${PLONE_VERSION} as builder

# Update and upgrade, always
RUN apt-get update && apt-get upgrade -y

# Remove Volto support from builder image
RUN /app/bin/pip uninstall -y plone.volto

# Use /app as the workdir
WORKDIR /app

# Copy the Logo
COPY ./img/Logo.svg /app/lib/python3.11/site-packages/plone/staticresources/static/icons/plone.svg
COPY ./img/Logo.svg /app/lib/python3.11/site-packages/Products/CMFPlone/browser/static/plone-logo.svg
# Copy the favicon
COPY ./img/favicon.ico /app/lib/python3.11/site-packages/zmi/styles/resources/logo/favicon/favicon.ico
COPY ./img/favicon.ico /app/lib/python3.11/site-packages/Products/CMFPlone/browser/static/favicon.ico
COPY ./img/favicon.ico /app/lib/python3.11/site-packages/plonetheme/barceloneta/theme/barceloneta-favicon.ico
# Footer
COPY ./templates/footer.pt /app/lib/python3.11/site-packages/Products/CMFPlone/browser/templates/footer.pt
COPY ./templates/footer.pt /app/lib/python3.11/site-packages/plone/app/portlets/browser/templates/footer.pt
COPY ./templates/colophon.pt /app/lib/python3.11/site-packages/plone/app/layout/viewlets/colophon.pt
COPY ./templates/colophon.pt /app/lib/python3.11/site-packages/Products/CMFPlone/browser/templates/colophon.pt

# Copy requirements.txt
COPY requirements.txt ./
# Copy additional zcml
COPY configure.zcml etc/package-includes/111-project-configure.zcml
COPY browser/login/configure.zcml /app/lib/python3.11/site-packages/Products/CMFPlone/browser/login/configure.zcml
COPY browser/configure.zcml /app/lib/python3.11/site-packages/Products/CMFPlone/browser/configure.zcml

# Install Easyfrom requirements
RUN ./bin/pip install -r /app/requirements.txt

FROM plone/server-prod-config:${PLONE_VERSION}

LABEL maintainer="BlackAlps (https://www.blackalps.ch/ba/index.php)" \
      org.label-schema.name="blackalps-ctf2024-fairytales-plone" \
      org.label-schema.description="BlackAlps - CTF 2024 - Fairy-TALES - Plone" \
      org.label-schema.vendor="Plone Community"

# Copy /app from builder
COPY --from=builder --chown=500:500 /app /app

# Link /data (the exposed volume) into /app/var
RUN ln -s /data /app/var

# Setup default type for site creation to be classic
ENV TYPE=classic