FROM python:3.13-slim

# Avoid interactive prompts and improve pip behavior
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is needed: the project uses hatch-vcs (version from git tags)
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the rest of the source (needed for editable install of the package)
COPY . .

# hatch-vcs needs a git repo with a tag to determine version.
# Initialize a repo and tag it so the build does not fail.
RUN git config --global user.email "dev@example.com" \
    && git config --global user.name "dev" \
    && git init -q \
    && git add -A \
    && git commit -qm "containerize" \
    && git tag v0.0.0

# Install the project in editable/dev mode with ALL optional deps + dev/test tooling
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
