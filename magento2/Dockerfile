FROM nginx:latest

ENV MAGENTO_VERSION 2.2.3
ENV INSTALL_DIR /code
ENV COMPOSER_HOME /code/.composer/

RUN apt-get update

RUN apt-get install -y curl php-cli php-mbstring git unzip

RUN curl -sS https://getcomposer.org/installer -o composer-setup.php && \
 php composer-setup.php --install-dir=/usr/local/bin --filename=composer

RUN chsh -s /bin/bash www-data

RUN cd /tmp && \
  curl https://codeload.github.com/magento/magento2/tar.gz/$MAGENTO_VERSION -o $MAGENTO_VERSION.tar.gz && \
  tar xvf $MAGENTO_VERSION.tar.gz && \
  cp -R magento2-$MAGENTO_VERSION/* $INSTALL_DIR/

RUN chown -R www-data:www-data $INSTALL_DIR
RUN su www-data -c "cd $INSTALL_DIR && composer install"
RUN su www-data -c "cd $INSTALL_DIR && composer config repositories.magento composer https://repo.magento.com/"

RUN cd $INSTALL_DIR \
    && find . -type d -exec chmod 770 {} \; \
    && find . -type f -exec chmod 660 {} \; \
    && chmod u+x bin/magento

RUN echo "memory_limit=2048M" > /usr/local/etc/php/conf.d/memory-limit.ini

RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

WORKDIR $INSTALL_DIR
