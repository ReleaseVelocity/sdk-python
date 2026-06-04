FROM python:3.12-slim

# hatch-vcs derives the package version from git tags. No git history is
# available in this build context, so provide a pretend version so the
# editable install can succeed. Set both env vars for safety.
ARG PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=${PRETEND_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION=${PRETEND_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# Build deps:
#   gcc + portaudio19-dev -> pyaudio (bidi extra)
#   libsndfile1           -> audio handling
#   git                   -> some tooling expects it on PATH
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        git \
        portaudio19-dev \
        libsndfile1 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest + source first for better layer caching.
COPY pyproject.toml README.md ./
COPY src/ ./src/

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain (pytest, ruff, mypy, moto, ...).
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the remainder of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
