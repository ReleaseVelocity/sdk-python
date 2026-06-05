FROM python:3.13-slim

# hatch-vcs determines the version from git tags. Since the source is copied
# without a .git directory, provide a pretend version so the build succeeds.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# portaudio is required to build/run the optional pyaudio (bidi) dependency,
# and git is useful for dev tooling / pre-commit.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the source package (needed for editable install resolution).
COPY src ./src

# Install the project in editable/dev mode with all runtime extras plus the
# dev and otel toolchains so tests, linters, and type checkers are available.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev,otel]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
