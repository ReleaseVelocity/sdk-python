FROM python:3.12-slim

# Build tooling needed by some native wheels (e.g. pyaudio is excluded, but be safe for others)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs normally derives the version from git tags. This workspace has no
# .git directory, so provide an explicit fallback version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0

RUN pip install --no-cache-dir --upgrade pip hatch hatchling hatch-vcs

# Copy the full source.
COPY . .

# Install the project in editable/dev mode with all extras + dev/test tooling.
# 'all' brings every model-provider extra; 'dev' brings pytest, ruff, mypy, moto, etc.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
