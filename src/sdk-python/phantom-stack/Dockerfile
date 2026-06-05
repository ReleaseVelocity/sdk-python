FROM python:3.11-slim

# hatch-vcs derives the version from git tags. Since the source tree has no
# git history here, provide a pretend version so the build backend succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# System deps:
# - git: hatch-vcs/version tooling expectations and general dev convenience
# - build-essential: some transitive deps may need to compile
# - portaudio19-dev: required by pyaudio (bidi extra), harmless otherwise
WORKDIR /app

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install the project in editable/development mode with all optional
# dependencies plus the dev (test/lint) toolchain.
# Copy the full source then install (editable install needs the package tree).
COPY . .

RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
