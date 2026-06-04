FROM python:3.11-slim

# hatch-vcs derives the version from git tags. This source tree is not a git
# checkout, so provide an explicit version to the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+dev
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# Minimal build tooling. Most wheels are prebuilt; gcc covers any source builds.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Layer caching: copy dependency manifest first.
COPY pyproject.toml README.md ./

# Copy the full source needed for an editable install.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the project in editable/development mode with ALL extras plus the
# dev (test/lint) toolchain.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
