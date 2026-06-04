FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the source is shipped
# without a .git directory, provide a pretend version so the build succeeds.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# Some optional dependencies (e.g. pyaudio under the bidi extra) need build
# tooling and portaudio headers. The `all` feature set used for testing does
# not include bidi, but we install build-essential to be safe for native wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install to resolve the package).
COPY src/ ./src/

# Install the project in editable/development mode with the full feature set
# (matching the hatch-test env `features = ["all"]`) plus dev/test tooling.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
