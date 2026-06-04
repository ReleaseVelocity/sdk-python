FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree may not be a
# git checkout in this environment, so provide a pretend version so the
# editable build succeeds without git metadata.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0.dev0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# git is useful for dev workflows; build tools kept minimal. portaudio/pyaudio
# (bidi extras) are NOT installed since they are excluded from the default
# test run and require system audio libs.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so hatchling's wheel target resolves during
# the dependency-only install layer.
COPY src/strands/__init__.py src/strands/__init__.py

# Install the project in editable/development mode with all optional
# features plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Now copy the full source tree (overlays the partial copy above).
COPY . .

# Re-run install to pick up any console scripts / metadata after full copy
# (editable install already points at /app/src, so this is cheap).
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
