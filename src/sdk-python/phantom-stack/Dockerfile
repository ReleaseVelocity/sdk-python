FROM python:3.12-slim

# hatch-vcs reads the version from git tags; the build context has no .git,
# so provide a pretend version to satisfy the dynamic versioning.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# System deps: build tools for any native wheels (e.g. pyaudio needs portaudio),
# and git in case tooling expects it.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the source package (needed for editable install to resolve packages).
COPY src ./src

# Install the project in editable/dev mode with all optional + dev dependencies.
# bidi extras depend on python>=3.12 markers which are satisfied here.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
