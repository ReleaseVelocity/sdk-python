FROM python:3.12-slim

# hatch-vcs reads the version from git tags. The build context has no .git
# directory, so provide a pretend version to let the build succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0.dev0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# portaudio is required to build pyaudio (pulled in by the bidi extra),
# git is handy for tooling. Install only what native builds need.
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Source must exist for an editable install of a hatchling project.
COPY src ./src

# Install the project in editable/development mode with every extra plus the
# dev/test tooling so a developer can run tests, linters, and type checks.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
