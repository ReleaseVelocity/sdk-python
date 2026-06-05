FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Provide a pretend version so the
# build does not require git history.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# portaudio is required to build pyaudio (pulled in by the optional bidi extras).
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    gcc \
    g++ \
    portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because pip builds the project in editable mode).
COPY src ./src

# Install the project in editable/development mode with all + dev dependencies.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
