FROM python:3.13-slim

# git is needed because hatch-vcs derives the version from git tags/history
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        portaudio19-dev \
        gcc \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the rest of the source (needed for editable install + VCS version)
COPY . .

# hatch-vcs needs a git repo with at least one tag/commit to resolve a version.
# If the source isn't a git repo (e.g. build context excludes .git), create a
# minimal repo so the editable install can resolve a fallback version.
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -qm "init" && \
        git tag v0.0.0 ; \
    fi

# Install the project in editable/dev mode with all optional + dev dependencies
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
