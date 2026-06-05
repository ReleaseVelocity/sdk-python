FROM python:3.12-slim

# pyaudio (bidi extra) needs portaudio headers; build tools for any native deps
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        g++ \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs determines version from git tags; ensure a git repo + tag exists
# so the editable install succeeds even without the original .git directory.
ENV PIP_DEFAULT_TIMEOUT=120 \
    PYTHONUNBUFFERED=1 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_BUILD_NO_HOOKS=false

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the full source
COPY . .

# Initialize a git repo with a tag so hatch-vcs can resolve a version
RUN git config --global --add safe.directory /app \
    && if [ ! -d .git ]; then \
         git init -q && \
         git config user.email "dev@example.com" && \
         git config user.name "dev" && \
         git add -A && \
         git commit -qm "init" && \
         git tag v0.0.0 ; \
       fi

# Install the project in editable/dev mode with ALL extras + dev/test tooling
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
