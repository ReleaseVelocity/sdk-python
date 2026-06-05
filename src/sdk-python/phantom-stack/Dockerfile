FROM python:3.12-slim

# System deps:
#  - git: hatch-vcs reads version from git tags
#  - build-essential: in case any optional dep needs to compile
#  - portaudio19-dev: required by pyaudio (bidi extra)
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git tags. The source tree has no .git,
# so provide a pretend version to the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create a minimal source tree so the editable build backend resolves,
# then install full dependency set. Source is bind-mounted/overwritten later.
COPY src ./src

RUN python -m pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project.
COPY . .

CMD ["sleep", "infinity"]
