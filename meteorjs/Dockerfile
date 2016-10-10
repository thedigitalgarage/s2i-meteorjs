FROM openshift/base-centos7

# This image provides a Node.JS environment you can use to run your Node.JS
# applications.

MAINTAINER Mateus Caruccio <mateus.caruccio@getupcloud.com>

EXPOSE 8080

# This image will be initialized with "npm run $NPM_RUN"
# See https://docs.npmjs.com/misc/scripts, and your repo's package.json
# file for possible values of NPM_RUN
ENV METEORJS_VERSION=4.1 \
    NPM_CONFIG_LOGLEVEL=info \
    NPM_CONFIG_PREFIX=$HOME/.npm-global \
    PATH=$HOME/.meteor:$PATH \
    NODE_ENV=production \
    DEV_MODE=false \
    USER=default

LABEL io.k8s.description="Platform for building and running MeteorJS applications" \
      io.k8s.display-name="MeteorJS v$METEORJS_VERSION" \
      io.openshift.expose-services="8080:http" \
      io.openshift.tags="builder,nodejs,meteorjs,meteorjs$METEORJS_VERSION" \
      com.redhat.deployments-dir="/opt/app-root/src"

# yum install -y centos-release-scl-rh && \
RUN set -ex && \
  INSTALL_PKGS="bzip2 nss_wrapper" && \
  yum install -y --setopt=tsflags=nodocs $INSTALL_PKGS && \
  rpm -V $INSTALL_PKGS && \
  yum clean all -y && \
  curl -sL https://install.meteor.com | sh

# Copy the S2I scripts from the specific language image to $STI_SCRIPTS_PATH
COPY ./s2i/bin/ $STI_SCRIPTS_PATH

# Each language image can have 'contrib' a directory with extra files needed to
# run and build the applications.
COPY ./contrib/ /opt/app-root

# Drop the root user and make the content of /opt/app-root owned by user 1001
RUN chown -R 1001:0 /opt/app-root
USER 1001

# Set the default CMD to print the usage of the language image
CMD $STI_SCRIPTS_PATH/usage
