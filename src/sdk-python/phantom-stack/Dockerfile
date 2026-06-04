FROM python:3.12-slim

# hatch-vcs derives the version from git tags/history, so git is required at build time.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the rest of the project (source, tests, git metadata for versioning).
COPY . .

# hatch-vcs requires git metadata to compute a version. If the build context
# has no git history, initialize a throwaway repo with a tag so the editable
# install succeeds.
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -q -m "init" && \
        git tag v0.0.0 ; \
    fi

# Upgrade pip tooling and install the project in editable/development mode with
# all optional model providers ("all") plus dev/test tooling ("dev").
RUN pip install --no-cache-dir --upgrade pip setuptools wheel && \
    pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
