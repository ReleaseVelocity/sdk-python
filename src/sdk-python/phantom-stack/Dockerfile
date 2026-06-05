FROM python:3.12-slim

# hatch-vcs (setuptools_scm) needs a version. Since the build context has no
# git metadata/tags, provide a pretend version so the editable install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is required by hatch-vcs at build time; build-essential is occasionally
# needed for native deps (pyaudio etc. are excluded since we don't install bidi).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so the editable install can resolve the package.
COPY src ./src

# Install the project in editable/dev mode with all optional + dev dependencies.
# 'all' pulls every model-provider extra; 'dev' pulls the test/lint toolchain.
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
