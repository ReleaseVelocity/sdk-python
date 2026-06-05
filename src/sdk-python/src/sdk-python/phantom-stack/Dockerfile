FROM python:3.12-slim

# hatch-vcs derives the package version from git tags. The build context has no
# .git directory, so provide a pretend version to allow the editable install.
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    HATCH_VCS_PRETEND_VERSION=${PKG_VERSION} \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy dependency manifest + package source first for better layer caching.
# The build backend reads packages from src/strands, so the source is needed
# during the editable install step.
COPY pyproject.toml README.md ./
COPY src ./src

# Install the project in editable/development mode with all optional features
# plus dev/test tooling (pytest, moto, mypy, ruff, hatch, etc.).
# The bidi extra is intentionally excluded: it requires system audio libraries
# (portaudio/pyaudio) and the project's default test config ignores bidi tests.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs, etc.).
COPY . .

CMD ["sleep", "infinity"]
