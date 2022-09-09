FROM ubuntu:20.04
MAINTAINER Greg Cockburn

# Update base packages
RUN apt update \
    && apt upgrade --assume-yes

# Install pre-reqs
RUN apt install --assume-yes --no-install-recommends gnupg

# Configure Zoneminder PPA
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys ABE4C7F993453843F0AEB8154D0BF748776FFB04 \
    && echo deb http://ppa.launchpad.net/iconnor/zoneminder-1.36/ubuntu focal main > /etc/apt/sources.list.d/zoneminder.list \
    && apt update

# Install zoneminder
RUN DEBIAN_FRONTEND=noninteractive apt install --assume-yes zoneminder \
    && a2enconf zoneminder \
    && a2enmod rewrite cgi

# Install msmtp
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y msmtp

## Install ES and Object Detect
RUN DEBIAN_FRONTEND=noninteractive apt install -y git wget vim python3 python3-pip sudo libcrypt-mysql-perl libcrypt-eksblowfish-perl libmodule-build-perl libyaml-perl build-essential cmake libjson-perl libgeos-dev libopenblas-dev liblapack-dev libblas-dev
RUN yes | perl -MCPAN -e "install Crypt::MySQL" && yes | perl -MCPAN -e "install Config::IniFiles" && yes | perl -MCPAN -e "install Crypt::Eksblowfish::Bcrypt" && yes | perl -MCPAN -e "install Net::WebSocket::Server" && yes | perl -MCPAN -e "install LWP::Protocol::https" && yes | perl -MCPAN -e "install Net::MQTT::Simple"
RUN cd /tmp/ && git clone https://github.com/zoneminder/zmeventnotification.git && cd zmeventnotification && ./install.sh --install-es --install-config --install-hook --no-pysudo --no-interactive
RUN pip3 install opencv-contrib-python==4.5.5.64

# Copy Configure
RUN cp -a /etc/zm /etc/zmbak

# Setup Volumes
VOLUME /var/cache/zoneminder/events /var/cache/zoneminder/images /var/lib/mysql /var/log/zm

# Expose http port
EXPOSE 80

# Configure entrypoint
COPY utils/entrypoint.sh /usr/local/bin/
RUN chmod 755 /usr/local/bin/entrypoint.sh
ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]
