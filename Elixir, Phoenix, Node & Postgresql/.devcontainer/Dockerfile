# Update the SOURCE and  VARIANT arg in docker-compose.yml
ARG SOURCE
ARG VARIANT
FROM ${SOURCE}:${VARIANT}

# This Dockerfile adds a non-root user with sudo access.
# Update the “remoteUser” property in devcontainer.json to use it.
# More info: https://aka.ms/vscode-remote/containers/non-root-user.
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Options for common package install script
ARG INSTALL_ZSH="true"
ARG UPGRADE_PACKAGES="true"

# Install needed packages and setup non-root user.
# Use a separate RUN statement to add your own dependencies.
COPY library-scripts/common-debian.sh /tmp/library-scripts/common-debian.sh
RUN /bin/bash /tmp/library-scripts/common-debian.sh "${INSTALL_ZSH}" \
  "${USERNAME}" "${USER_UID}" "${USER_GID}" "${UPGRADE_PACKAGES}" \
  && apt-get install -y build-essential \
  && apt-get install -y inotify-tools \
  && apt-get clean -y && rm -rf /var/lib/apt/lists/* /tmp/library-scripts

# [Optional] Settings for installing Node.js.
ARG NODE_VERSION="lts/*"
ENV NVM_DIR=/usr/local/share/nvm
ENV NVM_SYMLINK_CURRENT=true
ENV PATH=${NVM_DIR}/current/bin:${PATH}

COPY library-scripts/node-debian.sh /tmp/library-scripts/node-debian.sh
RUN /bin/bash /tmp/library-scripts/node-debian.sh \
  "${NVM_DIR}" "${NODE_VERSION}" "${USERNAME}" \
  && apt-get clean -y && rm -rf /var/lib/apt/lists/* /tmp/library-scripts

# Optional Settings for Phoenix
ARG PHOENIX_VERSION="1.5"

RUN su ${USERNAME} -c "mix local.hex --force \
  && mix local.rebar --force \
  && mix archive.install --force hex phx_new ${PHOENIX_VERSION}"

# [Optional] Uncomment this section to install additional OS packages.
# RUN apt-get update \
#   && export DEBIAN_FRONTEND=noninteractive \
#   && apt-get -y install --no-install-recommends <your-package-list-here>

# [Optional] Uncomment this line to install additional package.
# RUN  mix ...
