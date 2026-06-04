FROM python:3.12-slim

# System deps:
# - git: hatch-vcs derives version from git tags; portaudio for pyaudio (bidi extra)
# - build-essential for any native builds
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

RUN pip install --upgrade pip

# hatch-vcs derives the version from git tags. This source tree has no .git,
# so provide a pretend version for setuptools_scm (used under the hood).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0

# Copy full source
COPY . .

# Install the project in editable/dev mode with ALL optional + dev dependencies.
# "bidi-all" pulls in the bidirectional streaming extras; "dev" pulls test/lint tooling.
RUN pip install -e ".[all,bidi-all,dev]"

CMD ["bash"]
