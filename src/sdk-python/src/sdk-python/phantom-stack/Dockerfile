FROM python:3.12-slim

# hatch-vcs determines version from git tags; no .git is shipped, so provide a
# pretend version to allow the build/editable install to succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# System deps: git (for hatch-vcs / general tooling) and build essentials in case
# any wheel requires compilation (pyaudio is only in bidi extra which we skip).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install build backend so editable install resolves, then install the project
# with all runtime extras (excludes bidi which needs py>=3.12 native audio libs)
# plus the dev/test dependencies.
RUN pip install hatchling hatch-vcs

# Copy the full source.
COPY . .

# Editable install with all optional runtime providers + dev/test tooling.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
