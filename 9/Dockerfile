FROM ubuntu:16.04

ADD config/redis.config /etc/redis/redis.config
ADD start /start

ENV DEBIAN_FRONTEND=noninteractive \
    OV_PASSWORD=changemenaja

RUN apt-get update && \
    apt-get install software-properties-common --no-install-recommends -yq && \
    add-apt-repository ppa:mikesplain/openvas -y && \
    add-apt-repository ppa:mrazavi/openvas -y && \
    apt-get clean && \
    apt-get update && \
    apt-get install alien \
                    bzip2 \
                    curl \
                    dirb \
                    dnsutils \
                    libopenvas9-dev \
                    net-tools \
                    nikto \
                    nsis \
                    openssh-client \
                    openvas9 \
                    rpm \
                    rsync \
                    sendmail \
                    smbclient \
                    sqlite3 \
                    texlive-latex-base \
                    texlive-latex-extra \
                    texlive-latex-recommended \
                    w3af \
                    wapiti \
                    wget \
                    build-essential \
                    libssl-dev \
                    -yq && \
    apt-get upgrade -yq && \
    apt-get purge \
        texlive-pstricks-doc \
        texlive-pictures-doc \
        texlive-latex-extra-doc \
        texlive-latex-base-doc \
        texlive-latex-recommended-doc \
        software-properties-common \
        -yq && \
    apt-get autoremove -yq && \
    rm -rf /var/lib/apt/lists/*

RUN wget -q https://nmap.org/dist/nmap-7.70.tar.bz2 && \
    tar xf nmap-7.70.tar.bz2 && \
    cd nmap-7.70 && \
    ./configure && \
    make && \
    make install && \
    cd .. && \
    rm -rf nmap-7.70  && \
    rm nmap-7.70.tar.bz2

RUN wget -q https://github.com/Arachni/arachni/releases/download/v1.5.1/arachni-1.5.1-0.5.12-linux-x86_64.tar.gz && \
    tar -zxf arachni-1.5.1-0.5.12-linux-x86_64.tar.gz && \
    mv arachni-1.5.1-0.5.12-linux-x86_64.tar.gz /opt/arachni && \
    ln -s /opt/arachni/bin/* /usr/local/bin/ && \
    rm -rf arachni*

RUN mkdir -p /var/run/redis && \
    wget -q \
    https://gist.github.com/pich4ya/c609c6f2a0d0e64be301b713d27c3b47 \
      -O /openvas-check-setup && \
    chmod +x /openvas-check-setup && \
    sed -i 's/DAEMON_ARGS=""/DAEMON_ARGS="-a 0.0.0.0"/' /etc/init.d/openvas-manager && \
    sed -i 's/DAEMON_ARGS=""/DAEMON_ARGS="--mlisten 127.0.0.1 -m 9390 --gnutls-priorities=SECURE128:-AES-128-CBC:-CAMELLIA-128-CBC:-VERS-SSL3.0:-VERS-TLS1.0"/' /etc/init.d/openvas-gsa && \
    sed -i 's/PORT_NUMBER=4000/PORT_NUMBER=6443/' /etc/default/openvas-gsa && \
    greenbone-nvt-sync > /dev/null && \
    greenbone-scapdata-sync > /dev/null && \
    greenbone-certdata-sync > /dev/null && \
    wget http://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xml  && \
    openvas-portnames-update service-names-port-numbers.xml && \
    rm service-names-port-numbers.xml  && \
    BUILD=true /start && \
    service openvas-scanner stop && \
    service openvas-manager stop && \
    service openvas-gsa stop && \
    service redis-server stop

CMD /start
EXPOSE 6443 9390
