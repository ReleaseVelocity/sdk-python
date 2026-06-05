FROM python:3.12-slim

# Git is needed because the project uses hatch-vcs (version from git tags)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        portaudio19-dev \
        gcc \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Upgrade pip tooling
RUN pip install --no-cache-dir --upgrade pip setuptools wheel

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the rest of the source
COPY . .

# hatch-vcs determines the version from git tags. Initialize a repo with a
# tag so the editable install can resolve a version. SETUPTOOLS_SCM / hatch-vcs
# fallback is provided via env var as a safety net.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "phantom-stack build" && \
    git tag v0.0.0

# Install the project in editable/development mode with ALL extras and dev tools
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
