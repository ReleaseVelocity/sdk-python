FROM python:3.12-slim

# System deps:
# - git: hatch-vcs build backend inspects VCS; we also init a repo for versioning
# - build-essential: some optional deps may need compilation
# - portaudio19-dev: required by pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# hatch-vcs determines the version from git tags. The source tree is not a git
# repo, so we provide a pretend version so the build does not fail.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1
ENV PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create package dir so editable install metadata resolves, then install.
# We copy the full source before install because hatchling needs the package
# tree present and editable install points at it.
COPY . /app

# Initialize a git repo so hatch-vcs has something to read (belt-and-suspenders
# alongside the pretend version env var) and the version resolves cleanly.
RUN git init -q && git add -A && \
    git -c user.email=dev@example.com -c user.name=dev commit -qm "phantom-stack build" && \
    git tag v0.0.0

# Install the project in editable/development mode with ALL runtime extras plus
# the dev/test toolchain (pytest, moto, ruff, mypy, hatch, etc.).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
