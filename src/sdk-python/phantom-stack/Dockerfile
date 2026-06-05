FROM python:3.12-slim

# System deps: git is required because the build backend (hatch-vcs) derives
# the package version from git tags. portaudio headers allow optional bidi
# extras to build if ever needed.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the full source tree.
COPY . /app

# hatch-vcs needs git history to compute a version. The source was provided
# without a .git directory, so initialize a throwaway repo with a tag.
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -qm "snapshot" && \
        git tag v1.0.0 ; \
    fi

# Install the project in editable/development mode with all runtime extras
# plus the dev/test toolchain (pytest, ruff, mypy, moto, ...).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
