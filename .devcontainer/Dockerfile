FROM fedora:latest

# Install Node.js (required for Claude Code), Git, and essential tools
RUN dnf install -y nodejs git-all && \
    dnf clean all

# Create a non-root user
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && echo "$USERNAME ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

# Install Claude Code globally via npm
RUN npm install -g @anthropic-ai/claude-code

USER $USERNAME
WORKDIR /home/$USERNAME/workspace