FROM python:3.12-slim

# Build tooling needed for some native deps (e.g. pyaudio is excluded, but be safe for build wheels)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs (setuptools-scm) derives the version from git tags. Provide a
# pretend version so the build backend succeeds without a full git history.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev
ENV PIP_DISABLE_PIP_VERSION_CHECK=1

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install + version resolution).
COPY src ./src

# Install the project in editable/development mode with ALL optional deps,
# dev/test tooling.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
