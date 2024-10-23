FROM ubuntu:22.04 

RUN apt-get update && \
    apt-get install -y software-properties-common build-essential procps && \
    apt-get install -y telnet wget curl unzip zip vim sudo lsb-release bat && \
    apt-get install -y python-pip && \
    apt-get install -y python3-pip && \
    pip install --upgrade pip && \
    apt-get install -y \
      git gh\
      make \
      curl && \
    apt-get install -y \
      apt-transport-https \
      ca-certificates \
      curl \
      gnupg-agent \
      software-properties-common \
      npm



# Install Docker CLI
RUN curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add - && \
    add-apt-repository \
    "deb [arch=$(dpkg --print-architecture)] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable" && \
    apt-get update && \
    apt-get install -y docker-ce-cli

    
RUN update-alternatives --set iptables  /usr/sbin/iptables-legacy || true && \
    update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy || true && \
    update-alternatives --set arptables /usr/sbin/arptables-legacy || true

RUN rm /bin/sh && ln -s /bin/bash /bin/sh
RUN apt-get -qq -y install curl wget unzip zip vim


# Metadata as defined in the OCI image spec
LABEL maintainer="Sajid Moinuddin <sajid.moinuddin@foxtel.com.au>"
LABEL version="1.0"
LABEL description="Dockerfile for setting up a non-root user for bedrock java development"

# Source SDKMAN in all future shell sessions
RUN echo "source $SDKMAN_DIR/bin/sdkman-init.sh" >> /home/${USERNAME}/.bashrc

# RUN $SDKMAN_DIR/bin/sdk install java 17.0.0-open && $SDKMAN_DIR/bin/sdk install maven 3.9.6


ARG HUB_VERSION=2.12.4
RUN curl -L https://github.com/github/hub/releases/download/v2.12.4/hub-linux-amd64-${HUB_VERSION}.tgz -o /tmp/hub.tar.gz && \
    tar -xvzf /tmp/hub.tar.gz -C /tmp 

RUN     mv /tmp/hub-linux-amd64-${HUB_VERSION}/ /usr/local/hub && rm -rf /tmp/hub.tar.gz

RUN curl -Lo skaffold https://storage.googleapis.com/skaffold/releases/v1.12.1/skaffold-linux-amd64 && chmod +x skaffold && \
     mv skaffold /usr/local/bin

ENV HELM_VERSION 2.16.10

# Install Helm
RUN curl -LO https://get.helm.sh/helm-v${HELM_VERSION}-linux-amd64.tar.gz && \
  tar -zxvf helm-v${HELM_VERSION}-linux-amd64.tar.gz && \
   mv linux-amd64/helm /usr/local/bin/helm && \
  rm -rf helm-v${HELM_VERSION}-linux-amd64.tar.gz linux-amd64

# TODO: fix version
ARG KIND_VERSION=latest
RUN curl -Lo ./kind https://kind.sigs.k8s.io/dl/${KIND_VERSION}/kind-linux-amd64
RUN chmod +x ./kind
RUN  mv ./kind /usr/bin/kind


#TODO : lock version of kubectl
# Install kubectl
RUN curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" && \
  chmod +x kubectl && \
   mv kubectl /usr/local/bin/
  # Set alias for kubectl

  # Install kubens and kubectx
  RUN curl -LO https://github.com/ahmetb/kubectx/releases/download/v0.9.4/kubectx_v0.9.4_linux_x86_64.tar.gz && \
    tar -xvf kubectx_v0.9.4_linux_x86_64.tar.gz && \
    mv kubectx /usr/local/bin && \
    rm kubectx_v0.9.4_linux_x86_64.tar.gz

  RUN curl -LO https://github.com/ahmetb/kubectx/releases/download/v0.9.4/kubens_v0.9.4_linux_x86_64.tar.gz && \
    tar -xvf kubens_v0.9.4_linux_x86_64.tar.gz && \
    mv kubens /usr/local/bin && \
    rm kubens_v0.9.4_linux_x86_64.tar.gz

# Install krew
RUN set -x; cd "$(mktemp -d)" && \
  OS="$(uname | tr '[:upper:]' '[:lower:]')" && \
  ARCH="$(uname -m | sed -e 's/x86_64/amd64/' -e 's/\(arm\)\(64\)\?.*/\1\2/' -e 's/aarch64$/arm64/')" && \
  KREW="krew-${OS}_${ARCH}" && \
  curl -fsSLO "https://github.com/kubernetes-sigs/krew/releases/latest/download/${KREW}.tar.gz" && \
  tar zxvf "${KREW}.tar.gz" && \
  ./"${KREW}" install krew

RUN echo 'export PATH="/root/.krew/bin:$PATH"' >> ~/.bashrc
ENV PATH="/root/.krew/bin:${PATH}"      

RUN kubectl krew install stern

RUN npm install -g aws-cdk


RUN echo "alias k='kubectl'" >> ~/.bashrc
RUN echo "alias kns='kubens'" >> ~/.bashrc
RUN echo "alias stern='kubectl stern'" >> ~/.bashrc
RUN echo "alias cns='kubectl config view --minify --output 'jsonpath={..namespace}' && echo'" >> ~/.bashrc


#TODO: using nonroot user causes issue with accessing host docker sock 
# # Set up a non-root user
# ARG USERNAME=vscode
# ARG USER_UID=1000
# ARG USER_GID=$USER_UID

# RUN groupadd --gid $USER_GID $USERNAME \
#     && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME 

# RUN echo "vscode ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# ARG DOCKER_GID=999
# RUN groupmod -g $DOCKER_GID docker
# RUN usermod -aG docker $USERNAME

# # Set up subuid/subgid so that "--userns-remap=default" works out-of-the-box.
# RUN set -x && \
#     groupadd --system dockremap && \
#     adduser --system -g dockremap dockremap && \
#     echo 'dockremap:165536:65536' >> /etc/subuid && \
#     echo 'dockremap:165536:65536' >> /etc/subgid



# RUN  usermod -aG docker $USERNAME

# RUN  mkdir -p /etc/sudoers.d \
#     && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
#     && chmod 0440 /etc/sudoers.d/$USERNAME



# Switch to the non-root user
# USER $USERNAME

RUN echo "alias k='kubectl'" >> ~/.bashrc
RUN echo "alias kns='kubens'" >> ~/.bashrc

# Set environment variables
ENV SDKMAN_DIR="/home/${USERNAME}/.sdkman"
ENV PATH="${SDKMAN_DIR}/bin:${PATH}"

# Install SDKMAN
RUN curl -s "https://get.sdkman.io" | bash

# this SHELL command is needed to allow using source
SHELL ["/bin/bash", "-c"]    
RUN chmod a+x "$SDKMAN_DIR/bin/sdkman-init.sh"

# seems you need to put 'sdk install...'' lines in same RUN command as 'source...'.
RUN source "$SDKMAN_DIR/bin/sdkman-init.sh"   \
                && sdk install java 21.0.2-open  \
                && sdk install maven 3.9.7    
                # && sdk install scala 2.13.10



RUN echo export AWS_REGION=ap-southeast-2 >> ~/.bashrc
RUN echo export AWS_PROFILE=foxsports-gitops-dev >> ~/.bashrc


# Expose port 8080
EXPOSE 8080

# Example commands for installing Java and Maven
# Uncomment these lines if you want to install Java and Maven using SDKMAN
# RUN sdk install java 17.0.0-open && sdk install maven 3.9.6
