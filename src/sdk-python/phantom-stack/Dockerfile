FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context may not be a
# git repository, so provide a fallback version to satisfy the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+dev
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# Minimal build tooling. pyaudio (bidi extra) needs portaudio headers; git is
# useful for hatch/pre-commit workflows.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# The wheel target packages src/strands; create a placeholder so the editable
# install can resolve the package before the full source is copied.
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install the project in editable/development mode with ALL runtime extras plus
# the dev (test/lint) tooling.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Now copy the rest of the source tree.
COPY . .

CMD ["sleep", "infinity"]
