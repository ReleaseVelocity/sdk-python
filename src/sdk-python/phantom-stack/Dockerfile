FROM python:3.11-slim

# hatch-vcs derives the version from git tags. Provide a pretend version so the
# build does not require an initialized git repository with tags.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# pyaudio (bidi extra) needs portaudio headers; git kept for general dev use.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source so the editable install can resolve the package.
COPY src ./src

# Install the project in editable/development mode with ALL optional groups
# plus the dev/test dependencies (pytest, moto, ruff, mypy, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
