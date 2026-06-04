FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to satisfy the dynamic version resolution.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# portaudio19-dev is required to build pyaudio (used by the optional bidi extra).
# git is kept available for hatch-vcs / developer convenience.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the full source (needed for an editable install of the package).
COPY . .

# Install the project in editable/development mode with all runtime extras
# plus the dev/test tooling so tests and linters are available.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
