FROM mcr.microsoft.com/vscode/devcontainers/base:ubuntu-22.04

# Default value to allow debug server to serve content over GitHub Codespace's port forwarding service
ENV RAILS_DEVELOPMENT_HOSTS=".githubpreview.dev,.preview.app.github.dev,.app.github.dev"

# Install additional OS packages and PostgreSQL client.
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends \
    postgresql-client \
    software-properties-common \
    build-essential \
    libssl-dev \
    libreadline-dev \
    zlib1g-dev \
    libyaml-dev \
    libsqlite3-dev \
    sqlite3 \
    libxml2-dev \
    libxslt1-dev \
    libcurl4-openssl-dev \
    libffi-dev \
    nodejs \
    yarn \
    gnupg2 \
    curl

# Setup Firefox to do system testing.
RUN add-apt-repository ppa:mozillateam/ppa && \
    echo 'Package: *\nPin: release o=LP-PPA-mozillateam\nPin-Priority: 1001\n' | tee /etc/apt/preferences.d/mozilla-firefox && \
    apt update && \
    apt -y install firefox

# Install RVM
RUN curl -sSL https://get.rvm.io | bash -s stable

# Install Ruby
RUN /bin/bash -c "source /etc/profile.d/rvm.sh && rvm install 2.7.7 && rvm use 2.7.7 --default"

# Set up the app directory
WORKDIR /app

# Copy the Gemfile and Gemfile.lock into the image
COPY Gemfile Gemfile.lock ./

# Install gems
RUN /bin/bash -c "source /etc/profile.d/rvm.sh && gem install bundler && bundle install"

# Copy the rest of the application code into the image
COPY . .

# Precompile assets
RUN /bin/bash -c "source /etc/profile.d/rvm.sh && bundle exec rake assets:precompile"

# Set up the entrypoint
COPY entrypoint.sh /usr/bin/
RUN chmod +x /usr/bin/entrypoint.sh
ENTRYPOINT ["entrypoint.sh"]

EXPOSE 3000
