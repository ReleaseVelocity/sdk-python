FROM python:3.12-slim

# Avoid interactive prompts and speed up pip
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    # hatch-vcs needs a version; provide a fallback so build works without git tags
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_BUILD_NO_HOOKS=false

# System deps:
# - git: hatch-vcs reads version from git history
# - build-essential: some transitive deps may need compilation
# - portaudio19-dev: pyaudio (bidi extras) needs portaudio headers
WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the full project (source + tests) before install so editable install resolves packages
COPY . .

# Ensure a git repo exists so hatch-vcs can derive a version; tag if none present
RUN git init -q && git add -A && \
    git -c user.email=build@local -c user.name=build commit -q -m "build snapshot" && \
    git tag v0.0.0 2>/dev/null || true

# Install the project in editable/dev mode with all optional deps + dev tooling.
# "all" pulls every model-provider extra; "dev" adds pytest, moto, ruff, mypy, etc.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
