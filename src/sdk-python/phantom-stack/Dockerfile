FROM python:3.12-slim

# hatch-vcs (setuptools_scm) determines version from git tags. Since the build
# context has no .git, provide a pretend version so the editable install works.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Minimal system deps. pyaudio (bidi extra) needs portaudio headers/compiler.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Copy source so the editable install can resolve the package.
COPY src ./src

# Install the project in editable/development mode with ALL dependencies,
# including dev/test tooling and every optional feature set.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
