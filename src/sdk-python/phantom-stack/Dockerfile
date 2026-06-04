FROM python:3.12-slim

# hatch-vcs (setuptools_scm) derives the version from git tags. The source tree
# has no .git directory, so provide a pretend version to satisfy the build.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# pyaudio (pulled in by the bidi extras) needs PortAudio headers to build.
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git && \
    rm -rf /var/lib/apt/lists/*

# Copy the full project (build context is the project root) and install it in
# editable mode with all runtime extras plus dev/test tooling.
COPY . .

RUN pip install --upgrade pip && \
    pip install -e ".[all,dev,bidi,bidi-gemini,bidi-openai]"

CMD ["sleep", "infinity"]
