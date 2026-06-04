FROM python:3.12-slim

# hatch-vcs derives the version from git tags via setuptools-scm. Since the
# build context may not contain git history, provide a pretend version so the
# editable install works. setuptools-scm reads SETUPTOOLS_SCM_PRETEND_VERSION.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0

WORKDIR /app

# System deps: git for hatch-vcs/pre-commit; build-essential only if needed by
# native wheels. portaudio for the optional pyaudio (bidi) dependency.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Leverage layer caching: copy only the manifest first.
COPY pyproject.toml README.md ./

# Pre-install dependency closure (without the project itself) for caching.
# Use --no-build-isolation-friendly approach: install runtime + dev extras.
RUN pip install --no-cache-dir --upgrade pip

# Copy full source and install in editable/development mode with all extras + dev tools.
COPY . .

RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
