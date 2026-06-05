FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no .git in this build
# context, so provide a pretend version for setuptools-scm/hatch-vcs.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# PyAudio (bidi extra) needs portaudio headers; build tools help for any
# native wheels that lack prebuilt artifacts.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install the project in editable mode with all runtime extras plus dev/test
# tooling. "all" pulls every model-provider extra; "dev" pulls test+lint tools.
# Copy full source then install (editable build needs src present).
COPY . .

RUN pip install --upgrade pip && \
    pip install -e ".[all,bidi-all,dev]"

CMD ["sleep", "infinity"]
