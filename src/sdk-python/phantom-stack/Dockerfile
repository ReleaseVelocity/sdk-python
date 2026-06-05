FROM python:3.12-slim

# Allow hatch-vcs to determine a version even without real git tags.
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

WORKDIR /app

# git is needed by hatch-vcs (the build backend reads the repo for versioning)
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install resolution).
COPY src ./src

# Initialize a throwaway git repo with a tag so hatch-vcs can resolve a version.
RUN git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -q -m "init" \
    && git tag v0.0.0

# Install the project in editable/dev mode with ALL optional + dev dependencies.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, etc.).
COPY . .

CMD ["sleep", "infinity"]
