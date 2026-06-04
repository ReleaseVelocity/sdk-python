FROM python:3.12-slim

# Allow overriding the version reported by hatch-vcs (project versions from git tags)
ARG SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${SETUPTOOLS_SCM_PRETEND_VERSION}

# System deps:
# - git: hatch-vcs reads version from git tags
# - portaudio19-dev + gcc: needed to build pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the rest of the source
COPY . /app

# Initialize a git repo with a tag so hatch-vcs can resolve a version,
# unless the project already contains one.
RUN if [ ! -d /app/.git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -q -m "dev build" && \
        git tag "v${SETUPTOOLS_SCM_PRETEND_VERSION}"; \
    fi

# Upgrade pip tooling and install project in editable/dev mode with ALL deps
RUN python -m pip install --upgrade pip setuptools wheel && \
    pip install -e ".[all,dev]" && \
    pip install -e ".[bidi-all]"

CMD ["sleep", "infinity"]
