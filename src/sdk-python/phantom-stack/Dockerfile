FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version that hatch-vcs/setuptools-scm honors.
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Minimal build tooling; portaudio is only needed by the optional [bidi] extra
# (not part of [all]). Keep the image lean and only add what the install needs.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the actual package source (needed for editable install / metadata build).
COPY src/strands ./src/strands

# Install the project in editable/development mode with ALL runtime extras
# plus the dev/test tooling (pytest, ruff, mypy, moto, hatch, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
