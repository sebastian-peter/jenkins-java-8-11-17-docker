# base image
FROM jenkins/jenkins:lts-jdk17

USER root 

# Install the latest Docker CE binaries, add user `jenkins` to the docker group and install openjdk 8
RUN apt-get update && \
    apt-get -y --no-install-recommends install wget \
      apt-transport-https \
      ca-certificates \
      curl \
      gnupg2 \
      software-properties-common \
      xvfb \
      linux-headers-amd64 \
      openjfx \ 
      cmake \
      g++ \
      openssl \
      python3-dev \
      gfortran \
      build-essential \
      perl && \
    # compile and add eccodes
    curl https://confluence.ecmwf.int/download/attachments/45757960/eccodes-2.21.0-Source.tar.gz --output eccodes.tar.gz && \
    tar -zxvf eccodes.tar.gz && \
    cd eccodes-2.21.0-Source && \
    mkdir build && cd build && cmake .. && make && ctest && make install

# add docker and openjdk8 repo (debian buster comes w/o openjdk8)
RUN wget -O - https://packages.adoptium.net/artifactory/api/gpg/key/public | tee /etc/apt/keyrings/adoptium.asc && \
    echo "deb [signed-by=/etc/apt/keyrings/adoptium.asc] https://packages.adoptium.net/artifactory/deb $(awk -F= '/^VERSION_CODENAME/{print$2}' /etc/os-release) main" | tee /etc/apt/sources.list.d/adoptium.list && \
    wget -O - https://download.docker.com/linux/ubuntu/gpg | tee /etc/apt/keyrings/docker.asc && \
    echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list && \
    apt-get update && \
    apt-get -y --no-install-recommends install docker-ce docker-ce-cli containerd.io temurin-8-jdk temurin-11-jdk && \
    usermod -aG docker jenkins

#
# supervisord
#
RUN apt-get install --no-install-recommends -y supervisor && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    mkdir -p /var/log/supervisor
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

#
# Entrypoint
#
COPY docker-entrypoint.sh /usr/bin/docker-entrypoint.sh
RUN chmod +x /usr/bin/docker-entrypoint.sh

ENTRYPOINT ["/usr/bin/docker-entrypoint.sh"]
