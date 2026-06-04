FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the source is copied without
# a .git directory, provide a pretend version so the build backend can resolve it.
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# portaudio is required to build pyaudio (pulled in by the optional "bidi" extra,
# but install build deps up front so the full dependency set can compile if needed).
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching, plus the sources the
# build backend needs to resolve packages.
COPY pyproject.toml README.md ./
COPY src ./src

# Install the project in editable/development mode with the full optional
# dependency set ("all") and the dev/test tooling.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

# Keep the container running so developers can exec in and run tests/linters.
CMD ["sleep", "infinity"]
