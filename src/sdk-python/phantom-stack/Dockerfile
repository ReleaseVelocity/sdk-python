FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version (consumed by setuptools-scm/hatch-vcs).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# PyAudio (bidi extra) needs PortAudio headers + a compiler.
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        portaudio19-dev \
        libsndfile1 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source so the editable install can build the package.
COPY src ./src

# Install the project in editable/development mode with ALL extras + dev tooling.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
