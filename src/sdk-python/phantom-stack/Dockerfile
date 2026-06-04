FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include a populated .git, provide a fallback version so the build succeeds.
ARG HATCH_VCS_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION} \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# portaudio headers are required to build pyaudio (pulled in by the bidi extra).
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to maximize layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install metadata resolves.
COPY src ./src

# Install the project in editable/development mode with all runtime extras
# plus the dev/test tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["bash"]
