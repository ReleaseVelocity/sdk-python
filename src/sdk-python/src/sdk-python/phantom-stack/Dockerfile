FROM python:3.12-slim

# Git is needed because the build backend (hatch-vcs) derives the package
# version from git tags. We initialize a repo + tag at build time so the
# editable install can compute a version.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the source tree and tests.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# hatch-vcs requires git metadata to determine the version.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "init" && \
    git tag v1.0.0

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain.
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
