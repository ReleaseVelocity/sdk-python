FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is needed because hatch-vcs derives the version from git tags/history
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Pre-resolve dependencies layer (source copied next). Editable install needs the
# package source + git metadata, so the real install happens after full copy.
COPY . /app

# hatch-vcs requires git metadata to determine the version. If the project was
# copied without a .git directory, create a lightweight repo with a tag so the
# build does not fail on version resolution.
RUN if [ ! -d /app/.git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -qm "snapshot" && \
        git tag v0.0.0 ; \
    fi

# Install the project in editable/development mode with all optional deps + dev tooling
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
