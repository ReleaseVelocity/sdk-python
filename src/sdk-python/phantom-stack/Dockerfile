FROM python:3.12-slim

# hatch-vcs (setuptools-scm) derives the version from git tags. The build
# context has no .git, so provide a fallback version to the build backend.
ARG HATCH_VCS_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# System deps: build tooling for any native wheels, portaudio for the optional
# bidi (pyaudio) extra, and git for tooling that expects it.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install dependencies (without the project itself) to cache this layer.
# We install the full set of extras plus dev/test tooling.
RUN pip install --upgrade pip \
    && pip install \
        "boto3>=1.26.0,<2.0.0" \
        "botocore>=1.29.0,<2.0.0" \
        "docstring_parser>=0.15,<1.0" \
        "jsonschema>=4.0.0,<5.0.0" \
        "mcp>=1.11.0,<2.0.0" \
        "pydantic>=2.4.0,<3.0.0" \
        "typing-extensions>=4.13.2,<5.0.0" \
        "watchdog>=6.0.0,<7.0.0" \
        "opentelemetry-api>=1.30.0,<2.0.0" \
        "opentelemetry-sdk>=1.30.0,<2.0.0" \
        "opentelemetry-instrumentation-threading>=0.51b0,<1.00b0"

# Copy the full source tree.
COPY . .

# Install the project in editable/development mode with all dependency groups.
# [all] pulls in every model-provider + a2a + otel + docs extra; [dev] adds the
# test/lint/type tooling (pytest, ruff, mypy, moto, etc).
RUN pip install -e ".[all,dev,bidi,bidi-gemini,bidi-openai]"

CMD ["sleep", "infinity"]
