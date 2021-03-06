FROM ruby:2.3.3-slim

RUN apt-get clean && apt-get update \
   && apt-get install -y --no-install-recommends \
      file \
      git \
      nodejs \
   && rm -rf /var/lib/apt/lists/* \
   && ln -s /usr/bin/nodejs /usr/bin/node

ONBUILD ARG UID=1000
ONBUILD ARG APP_HOME=/app
ONBUILD ARG BUNDLE_OPTIONS='--without development test'
ONBUILD ARG SKIP_BUNDLE

ONBUILD RUN useradd --user-group --uid $UID app
ONBUILD RUN mkdir -p $APP_HOME && chown -R app:app $APP_HOME
ONBUILD WORKDIR $APP_HOME
ONBUILD COPY Gemfile $APP_HOME/
ONBUILD COPY Gemfile.lock $APP_HOME/
ONBUILD ADD vendor $APP_HOME/vendor

ONBUILD RUN apt-get clean && apt-get update && \
            runDeps=' \
              libmysqlclient18 \
              libpq5 \
              libsqlite3-0 \
              libxslt1.1 \
              libxml2 \
            ' && \
            buildDeps=' \
              autoconf \
              automake \
              g++ \
              gcc \
              patch \
              make \
              libbz2-dev \
              libc6-dev \
              liblzma-dev \
              libmagickcore-dev \
              libmagickwand-dev \
              libreadline-dev \
              libtool \
              libxslt-dev \
              libmysqlclient-dev \
              libpq-dev \
              libsqlite3-dev \
              libxml2-dev \
            ' && \
            apt-get install -y --no-install-recommends $buildDeps && \
            ( test "$SKIP_BUNDLE" = "yes" || \
              ( bundle install $BUNDLE_OPTIONS && \
                apt-get purge -y --auto-remove $buildDeps && \
                apt-get install -y --no-install-recommends $runDeps && \
                rm -rf /var/lib/apt/lists/* ))
