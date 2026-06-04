FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# contain git tags, provide a pretend version so the editable install succeeds.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# Build tooling needed by some transitive deps (e.g. native wheels). Kept minimal.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
        curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for the editable install to resolve packages).
COPY src ./src

# Install the project in editable/development mode with ALL extras + dev/test deps.
RUN pip install --upgrade pip setuptools wheel \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
