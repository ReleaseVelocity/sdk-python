FROM python:3.12-slim

# System deps:
# - git: hatch-vcs determines the package version from git tags
# - portaudio19-dev + gcc/build tools: required to build pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install / version discovery)
COPY src ./src

# hatch-vcs needs a git tag to derive the version. The source tree is not a
# git checkout, so create a minimal repo with a version tag before installing.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "containerize" && \
    git tag v1.0.0

# Install the project in editable/development mode with ALL dependencies,
# including model-provider extras, a2a, bidi voice support, and dev/test tools.
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -e ".[all,bidi-all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

# Re-commit any newly added files so editable install / vcs stays consistent
RUN git add -A && git commit -qm "full source" || true

CMD ["sleep", "infinity"]
