FROM python:3.12-slim

# hatch-vcs/setuptools_scm reads version from git tags; no git repo is present
# in the build context, so provide a pretend version to satisfy the build backend.
ARG PKG_VERSION=1.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# System deps:
# - build-essential: native compilation for pyaudio (bidi extra)
# - portaudio19-dev: required headers/libs for pyaudio
# - git: used by some dev tooling (pre-commit) and vcs version backend
WORKDIR /app

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

# Leverage layer caching: copy only the manifest first.
COPY pyproject.toml README.md ./

# Install build tooling and resolve dependency closure before copying source.
# We create the source tree placeholder so an editable install can succeed.
RUN pip install --upgrade pip hatchling hatch-vcs

# Copy the full project source.
COPY . /app

# Install the project in editable/development mode with ALL optional features
# plus dev/test tooling (pytest, ruff, mypy, moto, etc.) and bidi extras.
RUN pip install -e ".[all,dev,bidi,bidi-gemini,bidi-openai]"

CMD ["sleep", "infinity"]
