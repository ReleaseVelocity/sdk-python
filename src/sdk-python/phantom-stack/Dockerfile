FROM python:3.12-slim

# setuptools-scm/hatch-vcs needs a version; this is not a git repo with tags,
# so provide a pretend version for the build.
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION}
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# git is sometimes referenced by tooling; build tools occasionally needed for wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy manifest first for layer caching.
COPY pyproject.toml README.md ./

# Copy source (needed because install is editable and packages resolve from src/strands).
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install project in editable/dev mode with all extras + dev tooling.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
