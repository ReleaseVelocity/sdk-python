FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to allow the editable install to succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+dev
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# build-essential covers any native compilation for transitive deps.
# git is used by hatch-vcs tooling. portaudio19-dev supports pyaudio (bidi extra,
# not installed by [all] but kept available for dev work).
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Install all runtime + optional + dev dependencies in editable mode.
# Source is mounted/copied next; editable install picks up src/strands.
COPY src ./src
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs)
COPY . .

CMD ["python", "-m", "pytest", "--co", "-q"]
