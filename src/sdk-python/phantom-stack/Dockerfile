FROM python:3.12-slim

# System deps: git for hatch-vcs fallback, portaudio for pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs determines version from git tags; this project ships without a
# .git directory, so provide a pretend version (consumed by setuptools_scm
# which hatch-vcs builds upon).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# Copy manifest first for better layer caching of dependency installs.
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install.
COPY src ./src

# Install the project in editable/development mode with all runtime extras
# plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
