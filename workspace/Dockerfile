FROM laradock/workspace:2.5-7.3
# FROM laradock/workspace:2.2-7.2

#####################################
# Non-Root User: template
#####################################

# Add a non-root user to prevent files being created with root permissions on host machine.
ARG PUID=1000
ARG PGID=1000
ARG NON_ROOT_USER=template
ARG NON_ROOT_GROUP=template

ENV PUID ${PUID}
ENV PGID ${PGID}

USER root

RUN groupadd -g ${PGID} ${NON_ROOT_GROUP} \
  && useradd -u ${PUID} -g ${NON_ROOT_GROUP} -m ${NON_ROOT_USER}

RUN apt-get update -y \
    && apt-get upgrade -y \
    && apt-get install -y iputils-ping tcpdump jq php7.2-phpdbg php7.2-xdebug \
    && apt-get autoremove -y \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#####################################
# Set Timezone
#####################################

ARG TZ=UTC
ENV TZ ${TZ}
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

#####################################
# Composer:
#####################################

# Add the composer.json
COPY ./composer.json /home/${NON_ROOT_USER}/.composer/composer.json

# Make sure that ~/.composer belongs to template
RUN chown -R ${NON_ROOT_USER}:${NON_ROOT_GROUP} /home/${NON_ROOT_USER}/.composer

#####################################
# Crontab
#####################################

COPY ./crontab /etc/cron.d
RUN chmod -R 644 /etc/cron.d


#####################################
# Non-root
#####################################
USER ${NON_ROOT_USER}

#####################################
# User Aliases
#####################################
COPY ./aliases.sh /home/${NON_ROOT_USER}/aliases.sh
RUN echo "" >> ~/.bashrc && \
    echo "# Load Custom Aliases" >> ~/.bashrc && \
    echo "source ~/aliases.sh" >> ~/.bashrc && \
  	echo "" >> ~/.bashrc && \
  	sed -i 's/\r//' /home/${NON_ROOT_USER}/aliases.sh && \
  	sed -i 's/^#! \/bin\/sh/#! \/bin\/bash/' /home/${NON_ROOT_USER}/aliases.sh


# add ./vendor/bin to non-root user's bashrc (needed for phpunit)
RUN echo "" >> ~/.bashrc && \
    echo 'export PATH="/var/www/vendor/bin:$PATH"' >> ~/.bashrc

# RUN echo "" >> ~/.bashrc && \
    # echo 'alias art="php artisan"' >> ~/.bashrc && \
    # echo 'alias phpunit="$(pwd)/vendor/bin/phpunit"' >> ~/.bashrc

#
#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
#

USER root

# Set default work directory
WORKDIR /var/www
