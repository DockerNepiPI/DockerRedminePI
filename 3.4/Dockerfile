FROM debian:stretch
MAINTAINER Andreas Sekulski <andreas.sekulski@gmail.com>

# add our user and group first to make sure their IDs get assigned consistently, regardless of whatever dependencies get added
RUN groupadd -r redmine && useradd -r -g redmine redmine

# Install dependencies
RUN apt-get update \
      && apt-get install apache2 libapache2-mod-passenger libpq-dev nano wget ruby ruby-dev build-essential libmagickwand-dev imagemagick -y \
      && wget https://www.redmine.org/releases/redmine-3.4.2.tar.gz \
      && tar xfvz redmine-3.4.2.tar.gz \
      && rm redmine-3.4.2.tar.gz \
      && mv redmine-3.4.2 /var/lib/redmine

ADD database.yml /var/lib/redmine/config/database.yml

WORKDIR /var/lib/redmine

RUN gem install bundler -v 1.17.3 \
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

VOLUME ["/var/lib/redmine/files", "/var/lib/redmine/plugins", "/var/lib/redmine/config/"]

EXPOSE 80

ADD docker-entypoint.sh /docker-entypoint.sh
RUN chmod +x /docker-entypoint.sh
ENTRYPOINT ["/docker-entypoint.sh"]
