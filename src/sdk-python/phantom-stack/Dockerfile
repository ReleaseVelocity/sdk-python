FROM python:3.11-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so we provide a pretend version to the setuptools_scm-compatible backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

WORKDIR /app

# System deps: git (hatch-vcs probes for it) and build tooling for any
# native wheels that lack prebuilt binaries.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN pip install --upgrade pip

# Copy dependency manifest first for layer caching.
COPY pyproject.toml ./
COPY README.md ./

# Copy the package source so the editable install can resolve the package.
COPY src ./src

# Install the project in editable/development mode with all optional
# feature groups plus the dev/test tooling.
RUN pip install -e ".[dev,all]"

# Copy the remainder of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
