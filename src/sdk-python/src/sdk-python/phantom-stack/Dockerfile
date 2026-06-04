FROM python:3.12-slim

# hatch-vcs derives the version from git tags. This project is shipped without a
# .git directory, so provide a fallback version to satisfy the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# portaudio is required by pyaudio (bidi extras); build tools for any native deps.
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so the editable install can find it.
COPY src ./src

# Install the project in editable/development mode with all optional and dev
# dependencies so tests, linters, and every provider are available.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
