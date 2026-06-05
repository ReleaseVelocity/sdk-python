FROM python:3.11-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is needed because the project uses hatch-vcs (version from git tags)
RUN apt-get update && \
    apt-get install -y --no-install-recommends git build-essential portaudio19-dev && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# The source tree is required for an editable install
COPY src ./src

# hatch-vcs determines the version from git tags. The source has no .git here,
# so create a lightweight repo with a tag to provide a deterministic version.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "containerized build" && \
    git tag v0.0.0

# Install the project in editable/dev mode with ALL optional deps + dev/test tools.
RUN pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
