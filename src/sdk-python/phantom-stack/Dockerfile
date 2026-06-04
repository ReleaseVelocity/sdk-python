FROM python:3.12-slim

# hatch-vcs determines version from git tags; without a .git dir we must
# provide a fallback version so editable install does not fail.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# portaudio is required to build pyaudio (pulled in by the bidi extra).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for editable install metadata.
COPY src ./src

# Install the project in editable/development mode with all extras + dev tools.
# Note: bidi extras require python>=3.12 (satisfied here).
RUN pip install --no-cache-dir -e ".[all,dev,bidi-all]"

# Copy the rest of the project (tests, configs, etc.).
COPY . .

CMD ["sleep", "infinity"]
