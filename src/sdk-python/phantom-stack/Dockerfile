FROM python:3.12-slim

# hatch-vcs (setuptools-scm) needs a version; no git tags available in this build context
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# portaudio is required by pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install)
COPY src ./src

# Install the project in editable/development mode with all dependencies,
# dev/test tooling, and the bidi extras.
RUN pip install --no-cache-dir -e ".[all,bidi-all,dev]"

# Copy the remaining project files (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
