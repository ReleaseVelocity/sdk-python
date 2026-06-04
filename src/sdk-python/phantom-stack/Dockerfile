FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context has no
# git history, provide a pretend version so hatchling can build the package.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0.dev0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# System deps: git for any tooling that probes it, build basics only if needed.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install resolving the wheel target).
COPY src ./src

# Install the project in editable/development mode with ALL extras + dev tools.
# pyaudio (from the bidi extra) needs portaudio headers; install them so the
# full "[all]" + "[dev]" dependency set resolves cleanly.
RUN apt-get update \
    && apt-get install -y --no-install-recommends build-essential portaudio19-dev \
    && pip install --no-cache-dir -e ".[all,dev,bidi]" \
    && rm -rf /var/lib/apt/lists/*

# Copy the remaining project files (tests, configs).
COPY . .

CMD ["sleep", "infinity"]
