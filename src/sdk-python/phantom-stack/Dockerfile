FROM python:3.12-slim

# Build tools needed for some native deps (e.g. pyaudio/portaudio not required for [all,dev])
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# hatch-vcs derives the version from git tags. There is no git repo in this
# context, so provide an explicit version to setuptools_scm/hatch-vcs.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so the editable install can resolve the package.
COPY src ./src

# Install the project in editable/development mode with all optional and dev
# dependencies so tests, linters and type checkers are available.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
