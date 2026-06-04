FROM python:3.12-slim

# hatch-vcs derives the version from git tags; the build context has no git
# history, so provide a pretend version to allow the editable install to
# succeed. Both generic and package-specific variables are set for safety.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is used by some build/dev tooling (pre-commit). Keep the image lean.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Upgrade pip tooling first.
RUN pip install --no-cache-dir --upgrade pip setuptools wheel

# Copy dependency manifest + readme first to leverage layer caching. README.md
# is referenced by pyproject.toml (readme field) and required to build.
COPY pyproject.toml README.md ./

# Copy source so the editable install can resolve the package.
COPY src/ ./src/

# Install the project in editable/development mode with ALL model-provider
# extras plus the dev (test/lint) dependencies so a developer can run tests,
# linters, type checks, etc. inside the container.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remainder of the project (tests, configs, docs).
COPY . .

# Keep imports working; default to a sanity check.
CMD ["python", "-c", "import strands; print('strands imported OK')"]
