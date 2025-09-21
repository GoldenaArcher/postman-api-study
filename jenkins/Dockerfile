FROM jenkins/jenkins:lts-jdk17

USER root

# --- Install Docker CLI inside the Jenkins container ---
RUN apt-get update && apt-get install -y \
    ca-certificates curl gnupg && \
    install -d /etc/apt/keyrings && \
    curl -fsSL https://download.docker.com/linux/debian/gpg \
      | gpg --dearmor -o /etc/apt/keyrings/docker.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
      https://download.docker.com/linux/debian bookworm stable" \
      > /etc/apt/sources.list.d/docker.list && \
    apt-get update && apt-get install -y docker-ce-cli && \
    rm -rf /var/lib/apt/lists/*

# --- Install Node.js 18 + Newman (+ htmlextra) ---
# Nodesource 脚本会写入 sources.list 并做 apt update
RUN curl -fsSL https://deb.nodesource.com/setup_18.x | bash - && \
    apt-get install -y nodejs && \
    npm install -g newman newman-reporter-htmlextra && \
    npm cache clean --force && \
    rm -rf /var/lib/apt/lists/*

# --- Install Postman CLI ---
RUN curl -o- "https://dl-cli.pstmn.io/install/linux64.sh" | sh

# Map host docker group id so Jenkins can use the Docker socket
# Pass DOCKER_GID at build time if your host's 'docker' group has a different GID.
ARG DOCKER_GID=998
RUN groupadd -g ${DOCKER_GID} docker || true && \
    usermod -aG docker jenkins

# --- Preinstall plugins ---
COPY plugins.txt /usr/share/jenkins/ref/plugins.txt
RUN jenkins-plugin-cli --plugin-file /usr/share/jenkins/ref/plugins.txt

# Return to the regular Jenkins user
USER jenkins
