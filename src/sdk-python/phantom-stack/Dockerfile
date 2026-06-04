FROM python:3.11-slim

# Git is required because the project derives its version from VCS tags
# (hatch-vcs). We also keep build-essential available in case any optional
# dependency needs to compile native extensions.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to leverage Docker layer caching.
COPY pyproject.toml README.md ./

# Copy the rest of the source.
COPY . /app

# The project uses hatch-vcs to determine its version from git tags.
# Initialize a git repository with a tag so the build backend can resolve a
# version. This is build-time scaffolding only and does not touch project files.
RUN if [ ! -d /app/.git ]; then \
        git init -q /app && \
        git -C /app config user.email "dev@example.com" && \
        git -C /app config user.name "dev" && \
        git -C /app add -A && \
        git -C /app commit -qm "container build snapshot" && \
        git -C /app tag v0.0.0 ; \
    fi

# Upgrade pip and install the project in editable/development mode with ALL
# optional feature extras plus the dev/test tooling.
RUN python -m pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
