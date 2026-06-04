FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no .git in the build
# context, so provide an explicit version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Minimal build tooling; most wheels are prebuilt for cp312.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Copy source needed for an editable install (hatch wheel target = src/strands).
COPY src ./src

# Install the project in editable/dev mode with all extras + dev/test tooling.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
