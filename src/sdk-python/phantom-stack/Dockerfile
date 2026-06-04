FROM python:3.12-slim

# hatch-vcs determines the version from git tags. Since the build context may not
# include git metadata, provide a pretend version so the build/install succeeds.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+docker
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+docker
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+docker
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# PyAudio (bidi extra) needs portaudio headers; git for any vcs operations.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-install dependencies using the manifest. We create a minimal package layout
# so the editable/full install resolves; full source is copied next.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the project in editable mode with all optional + dev dependencies so a
# developer can run tests, linters, and type checks inside the container.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
