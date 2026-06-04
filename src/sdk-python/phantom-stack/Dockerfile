FROM python:3.12-slim

# hatch-vcs (setuptools-scm) derives the version from git tags; the build
# context has no git history, so provide a pretend version to allow the
# editable install to succeed. Both the generic and dist-specific variables
# are set for maximum compatibility.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0

# git is occasionally needed by build tooling / pre-commit; keep image lean.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Upgrade pip tooling first.
RUN pip install --no-cache-dir --upgrade pip setuptools wheel

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Copy source so the editable install can resolve the package.
COPY src/ ./src/

# Install the project in editable/development mode with ALL extras plus the
# dev (test/lint) dependencies so a developer can run tests, linters, etc.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remainder of the project (tests, configs, docs).
COPY . .

CMD ["python", "-c", "import strands; print('strands imported OK')"]
