FROM python:3.12-slim

# pyaudio (bidi extra) needs portaudio headers; git needed for hatch-vcs versioning
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for layer caching
COPY pyproject.toml README.md ./

# hatch-vcs derives version from git tags; provide a fallback version so the
# editable install does not fail when no git history is present.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_BUILD_NO_HOOKS=false

# Copy the full project (source needed for editable install of packages)
COPY . .

# Initialize a git repo with a tag so hatch-vcs can determine a version.
RUN git init -q && git config user.email dev@local && git config user.name dev \
    && git add -A && git commit -qm "build" && git tag v0.0.0

# Install in editable/dev mode with all optional + dev dependencies.
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
