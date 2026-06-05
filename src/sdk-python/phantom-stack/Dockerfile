FROM python:3.13-slim

# git is required because the project derives its version from VCS tags
# (hatch-vcs). build-essential covers any native builds (e.g. pyaudio deps
# are optional/bidi-only, but some transitive deps may need a compiler).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the rest of the source.
COPY . /app

# hatch-vcs needs a git repo with a tag to compute the package version.
# If the project isn't already a git repo, bootstrap one with an initial tag.
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -q -m "containerization snapshot" && \
        git tag v0.0.0 ; \
    fi

# Upgrade pip and install the project in editable/development mode with the
# full set of model-provider extras ("all") plus the dev/test tooling ("dev").
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -e ".[all,dev]"

# Keep the container alive for interactive development.
CMD ["sleep", "infinity"]
