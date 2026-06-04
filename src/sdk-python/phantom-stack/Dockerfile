FROM python:3.12-slim

# System deps: git (hatch-vcs version detection fallback), build tools for any
# native deps, and portaudio for pyaudio (bidi extras).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git tags. The build context has no git
# history, so provide a pretend version so the build backend succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source so editable install can resolve the package.
COPY src ./src

# Install the project in editable/development mode with ALL optional deps,
# dev deps, and otel. "all" pulls in every model provider integration.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
