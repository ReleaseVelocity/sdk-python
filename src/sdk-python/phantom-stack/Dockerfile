FROM python:3.12-slim

# hatch-vcs (setuptools_scm) needs a version; no .git is shipped, so pretend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+docker \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Minimal build tooling; most wheels are prebuilt but keep git for vcs fallback.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source needed for the editable install / version resolution.
COPY src ./src

# Install the project in editable/development mode with all features + dev tools.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
