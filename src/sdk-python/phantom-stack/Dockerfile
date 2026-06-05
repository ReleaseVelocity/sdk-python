FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context has no
# git history, provide a fallback version so the editable install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# portaudio19-dev/gcc are needed if the optional bidi/pyaudio extra is ever
# installed; build-essential covers any native wheels that lack manylinux builds.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the editable install resolves the
# package metadata / version at install time).
COPY src ./src

# Install the project in editable/development mode with ALL runtime extras and
# the dev/test toolchain (pytest, moto, ruff, mypy, hatch, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the test suites.
COPY tests ./tests
COPY tests_integ ./tests_integ

CMD ["sleep", "infinity"]
