FROM python:3.12-slim

# Strands Agents uses hatch-vcs for versioning from git tags.
# There is no .git in this build context, so provide a pretend version
# to allow the editable install to compute a version.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+dev
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

# Some optional deps (e.g. pyaudio under bidi) need build/system libs.
# The 'all' extra does not include 'bidi', so minimal build tooling is enough.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for the editable/build install.
COPY src ./src

# Install the project in editable/dev mode with all optional + dev deps.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
