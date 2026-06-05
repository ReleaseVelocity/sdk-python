FROM python:3.13-slim

# hatch-vcs (setuptools_scm) determines version from git tags. Since the build
# context may not contain git metadata, provide a pretend version so the build
# of the editable install succeeds.
ARG PRETEND_VERSION=1.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PRETEND_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Install build backend and resolve dependency metadata early. We create a
# minimal src layout so the editable metadata build can succeed before the
# full source is copied.
RUN python -m pip install --upgrade pip hatchling hatch-vcs

# Copy the full project source.
COPY . .

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN python -m pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
