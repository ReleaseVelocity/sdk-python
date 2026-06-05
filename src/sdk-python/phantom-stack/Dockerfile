FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There's no .git in the build
# context, so provide a pretend version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0.dev0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# portaudio19-dev / gcc are needed for the optional bidi extra (pyaudio).
# Keep the toolchain minimal; install only what native builds require.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the project installs in editable mode).
COPY src ./src

# Install the project in editable/development mode with ALL runtime extras
# plus the dev/test tooling so tests, linters, and type checks can run.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
