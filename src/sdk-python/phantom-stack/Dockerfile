FROM python:3.13-slim

# hatch-vcs derives the version from git tags. The workspace has no git history,
# so we provide a fallback version via setuptools-scm's pretend-version env var.
ARG STRANDS_VERSION=0.0.0+docker
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION} \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# git is needed by hatch-vcs/setuptools-scm during the editable build.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-resolve dependencies against an empty package tree so source changes
# don't force a full dependency reinstall. A minimal package stub is needed
# for the editable backend to succeed at this stage.
RUN mkdir -p src/strands && touch src/strands/__init__.py \
    && pip install -e ".[all,dev]"

# Now copy the real source and tests, then reinstall editable to wire it up.
COPY src/ ./src/
COPY tests/ ./tests/
COPY tests_integ/ ./tests_integ/

RUN pip install -e ".[all,dev]"

# Verify the package imports and tests are collectible at build time.
RUN python -c "import strands; print('strands import OK')"

CMD ["sleep", "infinity"]
