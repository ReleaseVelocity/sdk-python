FROM python:3.12-slim

# System deps: git is needed by hatch-vcs to determine version from tags
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifests + sources needed for editable install.
# hatch-vcs needs git history to compute the version, so copy .git too.
COPY pyproject.toml README.md ./
COPY src ./src

# Upgrade pip tooling
RUN pip install --no-cache-dir --upgrade pip setuptools wheel

# Copy the rest of the project (tests, configs, .git for version detection)
COPY . .

# hatch-vcs needs a git tag/commit; if .git missing or no tags, provide a fallback version
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0

# Install the project in editable/development mode with all + dev dependencies.
RUN pip install --no-cache-dir -e ".[dev,all]"

CMD ["sleep", "infinity"]
