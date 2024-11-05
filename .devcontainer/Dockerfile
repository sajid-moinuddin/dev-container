# Define version variables
ARG UBUNTU_VERSION=20.04
ARG HUB_VERSION=2.12.4
ARG SKAFFOLD_VERSION=v1.12.1
ARG HELM_VERSION=2.16.10
ARG KIND_VERSION=latest
ARG KUBECTX_VERSION=v0.9.4
ARG KREW_VERSION=latest
ARG JAVA_VERSION=21.0.2-open
ARG MAVEN_VERSION=3.9.7



FROM ubuntu:${UBUNTU_VERSION}

ENV DEBIAN_FRONTEND=noninteractive

# Install essential packages and dependencies
RUN apt-get update && \
    apt-get install -y \
       telnet \
        wget \
        curl \
        unzip 

RUN apt-get install -y zip \
        vim \
        bat \
        git \
        make    \ 
        apt-transport-https \
        ca-certificates \
        gnupg-agent \
        sudo \
        lsb-release 
RUN apt-get install -y software-properties-common        

RUN curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add - && \
                add-apt-repository \
                "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
                $(lsb_release -cs) \
                stable" && \
                apt-get update && \
                apt-get install -y docker-ce-cli


# Set iptables alternatives
RUN update-alternatives --set iptables /usr/sbin/iptables-legacy || true && \
    update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy || true && \
    update-alternatives --set arptables /usr/sbin/arptables-legacy || true

# Set bash as the default shell
RUN rm /bin/sh && ln -s /bin/bash /bin/sh

# Metadata as defined in the OCI image spec
LABEL maintainer="Sajid Moinuddin <sajid.moinuddin@foxtel.com.au>"
LABEL version="1.0"
LABEL description="Dockerfile for setting up a non-root user for bedrock java development"

# Install SDKMAN and set up environment variables
ARG USERNAME=vscode

# Create a new user and add to sudoers
RUN useradd -m -s /bin/bash ${USERNAME} && \
        echo "${USERNAME} ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# Allow the container to connect to the host Docker daemon
RUN groupadd -g 984 docker && usermod -aG docker ${USERNAME}

# Install kubectl
RUN curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" && \
    chmod +x kubectl && \
    mv kubectl /usr/local/bin/




# Switch to the new user
USER ${USERNAME}

ENV SDKMAN_DIR="/home/${USERNAME}/.sdkman"
ENV PATH="${SDKMAN_DIR}/bin:${PATH}"

# RUN curl -s "https://get.sdkman.io" | bash && \
#     echo "source $SDKMAN_DIR/bin/sdkman-init.sh" >> /home/${USERNAME}/.bashrc && \
#     source "$SDKMAN_DIR/bin/sdkman-init.sh" && \
#     sdk install java ${JAVA_VERSION} && \
#     sdk install maven ${MAVEN_VERSION}

ARG NVM_VERSION=0.39.1
ARG NODE_VERSION=14.17.0
# Install NVM (Node Version Manager)
RUN sudo wget  https://raw.githubusercontent.com/nvm-sh/nvm/v${NVM_VERSION}/install.sh && \
    sudo chmod +x install.sh && \
    ./install.sh && \
    sudo rm install.sh

# RUN source ~/.bashrc && nvm -- version && \
#      nvm install ${NODE_VERSION} && \
#      nvm use ${NODE_VERSION} && \
#      nvm alias default ${NODE_VERSION}
#     nvm install ${NODE_VERSION} && \
#     nvm use ${NODE_VERSION} && \
#     nvm alias default ${NODE_VERSION}
RUN export NVM_DIR="$HOME/.nvm" && \
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  && nvm --version && \
    nvm install ${NODE_VERSION} && \
    nvm use ${NODE_VERSION} && \
    nvm alias default ${NODE_VERSION}


# # Install GitHub Hub
# RUN curl -L https://github.com/github/hub/releases/download/v${HUB_VERSION}/hub-linux-amd64-${HUB_VERSION}.tgz -o /tmp/hub.tar.gz && \
#     tar -xvzf /tmp/hub.tar.gz -C /tmp && \
#     mv /tmp/hub-linux-amd64-${HUB_VERSION}/ /usr/local/hub && \
#     rm -rf /tmp/hub.tar.gz

# # Install Helm
# RUN curl -LO https://get.helm.sh/helm-v${HELM_VERSION}-linux-amd64.tar.gz && \
#     tar -zxvf helm-v${HELM_VERSION}-linux-amd64.tar.gz && \
#     mv linux-amd64/helm /usr/local/bin/helm && \
#     rm -rf helm-v${HELM_VERSION}-linux-amd64.tar.gz linux-amd64

# # Install Kind
# RUN curl -Lo ./kind https://kind.sigs.k8s.io/dl/${KIND_VERSION}/kind-linux-amd64 && \
#     chmod +x ./kind && \
#     mv ./kind /usr/bin/kind


# # Install kubens and kubectx
# RUN curl -LO https://github.com/ahmetb/kubectx/releases/download/${KUBECTX_VERSION}/kubectx_${KUBECTX_VERSION}_linux_x86_64.tar.gz && \
#     tar -xvf kubectx_${KUBECTX_VERSION}_linux_x86_64.tar.gz && \
#     mv kubectx /usr/local/bin && \
#     rm kubectx_${KUBECTX_VERSION}_linux_x86_64.tar.gz

# RUN curl -LO https://github.com/ahmetb/kubectx/releases/download/${KUBECTX_VERSION}/kubens_${KUBECTX_VERSION}_linux_x86_64.tar.gz && \
#     tar -xvf kubens_${KUBECTX_VERSION}_linux_x86_64.tar.gz && \
#     mv kubens /usr/local/bin && \
#     rm kubens_${KUBECTX_VERSION}_linux_x86_64.tar.gz

# # Install krew
# RUN set -x; cd "$(mktemp -d)" && \
#     OS="$(uname | tr '[:upper:]' '[:lower:]')" && \
#     ARCH="$(uname -m | sed -e 's/x86_64/amd64/' -e 's/\(arm\)\(64\)\?.*/\1\2/' -e 's/aarch64$/arm64/')" && \
#     KREW="krew-${OS}_${ARCH}" && \
#     curl -fsSLO "https://github.com/kubernetes-sigs/krew/releases/latest/download/${KREW}.tar.gz" && \
#     tar zxvf "${KREW}.tar.gz" && \
#     ./"${KREW}" install krew

# RUN echo 'export PATH="/${USERNAME}/.krew/bin:$PATH"' >> ~/.bashrc
# ENV PATH="/${USERNAME}/.krew/bin:${PATH}"

# RUN kubectl krew install stern








# # Set up aliases and AWS environment variables
RUN echo "alias k='kubectl'" >> ~/.bashrc && \
    echo "alias kns='kubens'" >> ~/.bashrc && \
    echo "alias stern='kubectl stern'" >> ~/.bashrc && \
    echo "alias cns='kubectl config view --minify --output 'jsonpath={..namespace}' && echo'" >> ~/.bashrc && \
    echo "export AWS_REGION=ap-southeast-2" >> ~/.bashrc && \
    echo "export AWS_PROFILE=foxsports-gitops-dev" >> ~/.bashrc

# # Expose port 8080
# EXPOSE 8080