FROM cgregistry.capgroup.com/ubuntu:focal
LABEL org.opencontainers.image.source https://github.com/open-itg/deven-u
 
ENV TZ=America/Los_Angeles
 
# Configure CG apt package libraries ----------------------------------------------------------------------------------
 
RUN apt-get update && \
    apt-get -y install ca-certificates
 
RUN echo 'deb [trusted=yes] https://cgrepo.capgroup.com/repository/apt-proxy/ focal main restricted universe multiverse' > /etc/apt/sources.list
RUN echo 'deb [trusted=yes] https://cgrepo.capgroup.com/repository/apt-proxy/ focal-updates main restricted universe multiverse' >> /etc/apt/sources.list
 
RUN apt-get clean && apt-get update
RUN apt-get -y install ca-certificates
RUN apt-get -y install sudo
 
# Install Utilities ---------------------------------------------------------------------------------------------------
 
RUN DEBIAN_FRONTEND=noninteractive apt -y install wget curl zsh unzip vim snap
 
# Install Git ---------------------------------------------------------------------------------------------------------
 
RUN apt -y install git
RUN git config --global http.sslCAInfo /cacert.pem
 
# Install Docker ------------------------------------------------------------------------------------------------------
 
RUN DEBIAN_FRONTEND=noninteractive apt -y install docker.io docker
RUN curl -L https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
RUN chmod +x /usr/local/bin/docker-compose
RUN ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
 
# Install Python ------------------------------------------------------------------------------------------------------
 
RUN DEBIAN_FRONTEND=noninteractive apt -y install python3.8 python3-pip
RUN pip config --user set global.index https://cgrepo.capgroup.com/repository/cgpypi/pypi
RUN pip config --user set global.index-url https://cgrepo.capgroup.com/repository/cgpypi/simple
RUN pip config --user set global.trusted-host cgrepo.capgroup.com
 
# Install NodeJS ------------------------------------------------------------------------------------------------------
 
RUN DEBIAN_FRONTEND=noninteractive apt -y install nodejs yarn npm
RUN npm config set registry https://cgrepo.capgroup.com/repository/cgnpm/   
 
# Install awscli v2 ---------------------------------------------------------------------------------------------------
 
RUN curl https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip -o awscliv2.zip && \
    unzip awscliv2.zip && \
    ./aws/install
 
## Install Terraform --------------------------------------------------------------------------------------------------
 
RUN wget https://releases.hashicorp.com/terraform/1.0.11/terraform_1.0.11_linux_amd64.zip
RUN unzip terraform_1.0.11_linux_amd64.zip
RUN mv terraform /usr/local/bin
 
# Install gimme-aws-creds ---------------------------------------------------------------------------------------------
 
RUN pip3 install --upgrade gimme-aws-creds
 
# Create vscode user --------------------------------------------------------------------------------------------------
 
RUN groupadd --gid 1000 vscode \
    && useradd --uid 1000 --gid 1000 -m vscode \
    && echo vscode ALL=\(root\) NOPASSWD:ALL >> /etc/sudoers \
    && chmod 0440 /etc/sudoers
 
RUN chown -R vscode /cacert.pem
RUN usermod -a -G docker vscode
ADD --chown=vscode:vscode dotfiles/* /home/vscode/
ADD --chown=vscode:vscode cg-scripts/* /usr/local/bin/
 
RUN git clone --depth=1 https://github.com/romkatv/powerlevel10k.git /home/vscode/powerlevel10k
 
# Setup vscode user --------------------------------------------------------------------------------------------------
 
USER vscode
 
SHELL ["/usr/bin/zsh", "-c"]
ENV SHELL /usr/bin/zsh
RUN git config --global http.sslCAInfo /cacert.pem
 
RUN wget -O - https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash
RUN source ~/.zshrc && nvm install 16.10.0
 
CMD [ "sleep", "infinity"]
 
 
