FROM python:3.12-slim

# Install git (required by hatch-vcs to determine version from tags)
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Upgrade pip tooling
RUN pip install --no-cache-dir --upgrade pip setuptools wheel

# Copy dependency manifest first for layer caching
COPY pyproject.toml README.md ./

# Copy the rest of the source
COPY . /app

# hatch-vcs derives the version from git tags. Since the build context may not
# include a .git directory, initialize a throwaway repo with a tag so the build
# can determine a version. HATCH_VCS_PRETEND_VERSION is the fallback if needed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -qm "init" && \
        git tag v0.0.0 ; \
    fi

# Install the project in editable/development mode with ALL extras and dev tools
RUN pip install --no-cache-dir -e ".[all]" -e ".[dev]"

CMD ["sleep", "infinity"]
