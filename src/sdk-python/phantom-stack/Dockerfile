FROM python:3.12-slim

# portaudio is needed to build pyaudio (bidi extra); git for hatch-vcs versioning
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git tags; this repo has none in the build
# context, so provide a pretend version to allow an editable install to succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source so editable install can resolve packages
COPY src/ ./src/

# Install the project in editable/development mode with ALL extras + dev tools
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, etc.)
COPY . .

CMD ["sleep", "infinity"]
