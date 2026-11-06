# Minimal worker image: bare Kali base + exactly ONE agent CLI (build-arg AGENT).
# No metapackages, no PoC/knowledge repos, no cloud CLIs, no browser — agents
# self-install whatever a task needs (passwordless sudo is preconfigured, and
# AGENTS.md in the workspace tells them how).

ARG KALI_BASE=kalilinux/kali-rolling:latest
FROM --platform=amd64 ${KALI_BASE}

# Agent CLI to bake in: claudecode | codex | pi
ARG AGENT=claudecode

ENV TZ=Asia/Shanghai \
    NPM_CONFIG_DANGEROUSLY_ALLOW_ALL_SCRIPTS=true \
    CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC=1

# Pin apt to Kali's own CDN: the http.kali.org redirector intermittently serves dead mirrors
RUN sed -i 's|http://http.kali.org/kali|http://kali.download/kali|' /etc/apt/sources.list.d/kali.sources && \
    echo 'Acquire::Retries "3";' > /etc/apt/apt.conf.d/80-retries && \
    apt update && \
    apt -y install --no-install-recommends ca-certificates curl git nodejs npm sudo passwd procps tmux && \
    apt clean && \
    rm -rf /var/lib/apt/lists/*

RUN useradd -m -s /bin/bash kali && \
    echo "kali ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/kali && \
    chmod 440 /etc/sudoers.d/kali && \
    mkdir -p /home/kali/workspace && \
    chown -R kali:kali /home/kali

# Bake in exactly one agent CLI and verify it runs at build time
RUN case "$AGENT" in \
      claudecode) npm install -g @anthropic-ai/claude-code@latest && claude --version ;; \
      codex)      npm install -g @openai/codex@latest && codex --version ;; \
      pi)         npm install -g @mariozechner/pi-coding-agent@latest && pi --version ;; \
      *) echo "unsupported AGENT=$AGENT (use: claudecode|codex|pi)" >&2; exit 1 ;; \
    esac

USER kali
WORKDIR /home/kali/workspace
RUN git init

COPY .agents /home/kali/workspace/.agents
COPY .agents /home/kali/workspace/.claude
COPY AGENTS.md /home/kali/workspace/AGENTS.md
COPY AGENTS.md /home/kali/workspace/CLAUDE.md
