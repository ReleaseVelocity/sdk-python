FROM python:3.12-slim

# hatch-vcs derives the version from git tags; no .git is present in the build
# context, so provide an explicit version to the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# PyAudio (pulled by the optional "bidi" extra) needs portaudio headers, and a
# C toolchain is needed for any deps without prebuilt wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy the full project (editable install needs the source tree).
COPY . .

# Install the project in editable/dev mode with all runtime + dev dependencies.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
