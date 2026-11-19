FROM jenkins/jenkins:lts-jdk21
USER root

RUN apt-get update
RUN apt-get install -y --no-install-recommends python3 python3-venv python3-pip openssh-client ca-certificates curl && rm -rf /var/lib/apt/lists/*
 
RUN python3 -m venv /opt/ansible
RUN /opt/ansible/bin/pip install --no-cache-dir ansible
ENV PATH="/opt/ansible/bin:${PATH}"
 
USER jenkins
RUN jenkins-plugin-cli --plugins ansible ansicolor timestamper git workflow-aggregator credentials-binding