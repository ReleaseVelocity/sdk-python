FROM python:3.12-slim

# portaudio is needed for optional pyaudio (bidi extras); keeps full dep install working.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# hatch-vcs/setuptools_scm derives the version from git tags. The source tree has no
# .git, so provide a pretend version to allow an editable install.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install metadata can resolve.
COPY src ./src

# Install the project in editable/dev mode with all extras + dev/test tooling.
RUN pip install -e ".[all,dev]"

# Copy the rest of the source tree.
COPY . .

CMD ["sleep", "infinity"]
