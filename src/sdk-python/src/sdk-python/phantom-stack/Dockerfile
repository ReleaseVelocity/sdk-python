FROM python:3.12-slim

# Avoid interactive prompts and improve Python behavior in containers
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    # hatch-vcs uses setuptools_scm under the hood. There is no .git in the
    # build context, so provide a pretend version so the build succeeds.
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# System build deps: portaudio is required by pyaudio (bidi extra),
# build-essential/gcc for any native wheels that lack manylinux builds.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
        curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Leverage layer caching: copy dependency manifest + sources needed for an
# editable install before installing. The package uses src-layout, so the
# build backend needs the source tree present.
COPY pyproject.toml README.md ./
COPY src ./src

# Install the project in editable/development mode with ALL optional features
# (model providers, a2a, otel, docs) plus the dev/test toolchain.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs, etc.)
COPY . .

CMD ["sleep", "infinity"]
