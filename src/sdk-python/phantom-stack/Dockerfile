FROM python:3.12-slim

# Avoid interactive prompts and enable clean pip behavior
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs (via setuptools-scm) derives the version from git tags. There is no
# git repo in this build context, so provide a pretend version so the editable
# build succeeds. Set every variant the tooling may consult.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0

WORKDIR /app

# Minimal build/runtime deps. portaudio19-dev + gcc cover optional native
# extras (pyaudio) should a developer choose to install the bidi feature set.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install.
COPY src ./src

# Install the project in editable/development mode with the full feature set
# ([all]) plus the dev/test tooling ([dev]).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs, etc.).
COPY . .

CMD ["sleep", "infinity"]
