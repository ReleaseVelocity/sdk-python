FROM python:3.12-slim

# System deps:
# - git: hatch-vcs reads version from git tags during editable install
# - build-essential: in case any dependency needs native compilation
# - portaudio19-dev: required by pyaudio (bidi extra)
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest + sources needed for editable install.
# hatch-vcs needs the .git directory to determine version, so the whole
# project (including .git) is copied via compose volume at runtime; for the
# image build we copy the project tree.
COPY . /app

# hatch-vcs requires a valid version. If .git is unavailable at build time,
# provide a fallback version so the editable install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0

RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
