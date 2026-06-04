FROM python:3.12-slim

# hatch-vcs/setuptools_scm needs a version when there is no git history.
# Allow override via build arg so different environments can supply a real version.
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION}

# pyaudio (bidi extra) needs portaudio headers; git is handy for tooling/pre-commit.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

RUN pip install --upgrade pip

# Copy the full source tree (needed for editable install + tests).
COPY . .

# Install the project in editable/development mode with ALL optional features,
# dev/test tooling, and the bidi extras so every project command is runnable.
RUN pip install -e ".[all,dev]" \
    && pip install prompt_toolkit pyaudio websockets google-genai

CMD ["sleep", "infinity"]
