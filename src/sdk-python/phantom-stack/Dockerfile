FROM python:3.12-slim

# hatch-vcs determines the version from git tags. Since the build context may not
# include git history, provide a pretend version so the build/install succeeds.
ARG PKG_VERSION=1.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# System deps:
# - git: hatch-vcs may probe for it
# - portaudio19-dev: required to build/use pyaudio (bidi extra)
# - build-essential: in case any dependency needs native compilation
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so editable install can resolve the package.
COPY src ./src

# Install the project in editable/development mode with ALL dependencies,
# dev/test tooling, and bidi extras so every project command is available.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev,bidi,bidi-gemini,bidi-openai]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
