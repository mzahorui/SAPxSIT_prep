# syntax=docker/dockerfile:1
FROM mcr.microsoft.com/vscode/devcontainers/javascript-node:0-20

ENV DEBIAN_FRONTEND=noninteractive

# 1. Install System Tools
# Removed 'neovim' from apt-get to avoid installing the old v0.7 version
RUN apt-get update && apt-get install -y --no-install-recommends \
    git curl wget zsh gnupg apt-transport-https software-properties-common build-essential \
    python3 python3-venv python3-pip unzip gzip tar trash-cli ripgrep universal-ctags \
    fd-find fzf tmux openjdk-17-jdk sqlite3 \
    && apt-get clean -y && rm -rf /var/lib/apt/lists/* \
    && ln -s $(which fdfind) /usr/local/bin/fd

# 2. Install Latest Neovim (CLI Tool only)
# We install the latest binary so your terminal 'nvim' supports modern plugins (Lazy, Telescope, etc.)
# RUN curl -LO https://github.com/neovim/neovim/releases/latest/download/nvim-linux64.tar.gz \
#     && tar -C /usr/local -xzf nvim-linux64.tar.gz \
#     && rm nvim-linux64.tar.gz \
#     && ln -s /usr/local/nvim-linux64/bin/nvim /usr/local/bin/nvim

# 3. Install Cloud Foundry CLI (v8)
RUN wget -q -O - https://packages.cloudfoundry.org/debian/cli.cloudfoundry.org.key | gpg --dearmor -o /usr/share/keyrings/cli.cloudfoundry.org.gpg \
    && echo "deb [signed-by=/usr/share/keyrings/cli.cloudfoundry.org.gpg] https://packages.cloudfoundry.org/debian stable main" | tee /etc/apt/sources.list.d/cloudfoundry-cli.list \
    && apt-get update && apt-get install -y cf8-cli \
    && apt-get clean -y && rm -rf /var/lib/apt/lists/*

# 4. Install Binaries (Lazygit, Lazydocker, Zoxide)
RUN LAZYGIT_VERSION=$(curl -s "https://api.github.com/repos/jesseduffield/lazygit/releases/latest" | grep -Po '"tag_name": *"v\K[^"]*') \
    && curl -Lo lazygit.tar.gz "https://github.com/jesseduffield/lazygit/releases/latest/download/lazygit_${LAZYGIT_VERSION}_Linux_x86_64.tar.gz" \
    && tar xf lazygit.tar.gz lazygit && install lazygit /usr/local/bin && rm lazygit.tar.gz lazygit

RUN curl https://raw.githubusercontent.com/jesseduffield/lazydocker/master/scripts/install_update_linux.sh | bash \
    && mv $HOME/.local/bin/lazydocker /usr/local/bin/

RUN curl -sS https://raw.githubusercontent.com/ajeetdsouza/zoxide/main/install.sh | bash \
    && mv $HOME/.local/bin/zoxide /usr/local/bin/

# 5. User Configuration
USER node
WORKDIR /home/node

# SAP Tools
RUN npm install -g @sap/cds-dk @ui5/cli mbt yo @google/gemini-cli

# Install Zsh Plugins & Tmux Plugin Manager
# (Oh My Zsh is already installed in this base image at ~/.oh-my-zsh)
RUN git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions \
    && git clone https://github.com/joshskidmore/zsh-fzf-history-search ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-fzf-history-search \
    && git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm

ENV DEBIAN_FRONTEND=dialog
