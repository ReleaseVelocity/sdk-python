FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree provided has no
# git metadata, so we pin a version via the setuptools_scm/hatch-vcs env var.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# System deps:
#  - git: hatch-vcs build backend invokes git (we also init a repo below)
#  - portaudio19-dev + gcc/build tools: required to compile pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
        libffi-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Copy source needed for the editable build (hatch builds the wheel target).
COPY src ./src

# Initialize a throwaway git repo so hatch-vcs has a clean tree to work with,
# then install the project in editable/development mode with ALL extras + dev
# tooling so a developer can run tests, linters and type checks in-container.
RUN git init -q && git add -A && git -c user.email=dev@local -c user.name=dev commit -qm init \
    && git tag v0.0.0 \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["python", "-c", "import strands; print('strands import OK')"]
