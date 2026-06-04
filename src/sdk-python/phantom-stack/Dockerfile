FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Provide a fallback so the
# editable install succeeds even without git history available in the build.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# PyAudio (bidi extra) needs portaudio headers + a compiler.
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        git \
        portaudio19-dev \
        libsndfile1 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install metadata resolves,
# then install all runtime + dev/test dependencies.
COPY src/ ./src/

RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the remainder of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
