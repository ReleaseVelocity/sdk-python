FROM python:3.13-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include git history, provide a pretend version so the build/install succeeds.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# System deps:
# - git: hatch-vcs versioning support
# - portaudio19-dev / libasound2-dev: required to build pyaudio (bidi extra),
#   matching the project's CI for Linux. Kept so the full dev environment works.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Install build tooling and project (editable) with ALL feature + dev deps.
# `all` covers every model provider + otel + docs; `dev` covers test/lint tools.
RUN pip install --no-cache-dir hatch uv \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the source.
COPY . .

# Re-run editable install so the package metadata reflects the full source tree.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
