FROM resin/rpi-raspbian
MAINTAINER Andreas Sekulski <andreas.sekulski@gmail.com>

# add our user and group first to make sure their IDs get assigned consistently, regardless of whatever dependencies get added
RUN groupadd -r redmine && useradd -r -g redmine redmine

# Install dependencies
RUN apt-get update \
      && apt-get install gcc build-essential zlib1g zlib1g-dev zlibc ruby-zip libssl-dev libyaml-dev libcurl4-openssl-dev ruby gem libapache2-mod-passenger apache2-mpm-prefork apache2-dev libapr1-dev libxslt1-dev checkinstall libxml2-dev ruby-dev vim libmagickwand-dev imagemagick wget postgresql-client postgresql-client-common postgresql-contrib libpq-dev \
      && wget https://www.redmine.org/releases/redmine-3.4.2.tar.gz \
      && tar xfvz redmine-3.4.2.tar.gz \
      && rm redmine-3.4.2.tar.gz \
      && mv redmine-3.4.2 /var/lib/redmine

ADD database.yml /var/lib/redmine/config/database.yml

WORKDIR /var/lib/redmine

RUN gem install bundler \
      && bundle install --without development test \
      && bundle exec rake generate_secret_token

RUN RAILS_ENV=production bundle exec rake db:migrate

RUN RAILS_ENV=production REDMINE_LANG=de bundle exec rake redmine:load_default_data

RUN RAILS_ENV=production bundle exec rake redmine:plugins:migrate RAILS_ENV=production

RUN RAILS_ENV=production bundle exec rake tmp:cache:clear tmp:sessions:clear

RUN chown redmine:redmine Gemfile.lock \
      && ln -s /var/lib/redmine/public /var/www/html/redmine \
      && chown -R redmine:redmine files log tmp public/plugin_assets \
      && chown -R redmine:redmine /var/www/html/redmine \
      && chmod -R 755 files log tmp public/plugin_assets

# Apache2 configuration
ADD redmine.conf /etc/apache2/sites-available/redmine.conf
ADD passenger.conf /etc/apache2/mods-available/passenger.conf

RUN a2dissite 000-default.conf \
      && a2ensite redmine.conf \
      && service apache2 restart

VOLUME /var/lib/redmine/files

EXPOSE 443 80

ADD docker-entypoint.sh /docker-entypoint.sh
RUN chmod +x /docker-entypoint.sh
ENTRYPOINT ["/docker-entypoint.sh"]
