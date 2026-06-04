FROM python:3.12-slim

# hatch-vcs needs a version; no .git is shipped, so provide a pretend version
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Minimal build/runtime deps. portaudio for pyaudio (bidi extra), git for tooling.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for layer caching
COPY pyproject.toml README.md ./

# Copy the package source needed for editable install
COPY src/strands ./src/strands

# Install the project in editable/dev mode with all extras + dev tooling.
# Using pip directly (rather than hatch) so all deps land in the system env.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev,bidi-all]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
