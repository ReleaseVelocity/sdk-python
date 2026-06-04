FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# contain git metadata, provide a pretend version so the editable install works.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0 \
    HATCH_VCS_PRETEND_VERSION=1.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# Build tooling occasionally needed for native wheels (e.g. pyaudio for bidi).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install can resolve the package
# metadata even before the full source tree is copied.
RUN mkdir -p src/strands

# Install the project in editable/development mode with the full dependency set:
# every model provider extra (all), the bidirectional streaming extras (bidi-all),
# and the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
COPY src ./src
RUN pip install --upgrade pip \
    && pip install -e ".[all,bidi-all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
