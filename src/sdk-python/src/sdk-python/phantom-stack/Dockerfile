FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no .git in the build
# context, so provide a pretend version to the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# portaudio is required to build pyaudio (pulled in via bidi extras / all).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install resolution).
COPY src ./src

# Install the project in editable/development mode with all + dev dependencies.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
