FROM python:3.12-slim

# System deps:
# - git: required by hatch-vcs build backend (version from VCS)
# - build-essential: in case any dependency needs native compilation
# - portaudio19-dev: required by pyaudio (pulled in via bidi extras, harmless otherwise)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs/setuptools-scm derives the version from git tags. The source tree
# has no .git directory here, so provide an explicit fallback version so the
# editable install does not fail.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+docker
ENV PIP_DISABLE_PIP_VERSION_CHECK=1

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so the editable install can resolve packages.
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
