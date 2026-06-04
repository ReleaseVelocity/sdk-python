FROM python:3.12-slim

# Avoid interactive prompts and enable predictable Python behavior
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs derives the version from git tags. There is no git history in this
# build context, so provide a pretend version for the hatchling build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    HATCH_VCS_PRETEND_VERSION=0.0.0+dev

# System deps: git (used by hatch-vcs), and build tools only if needed by wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Layer caching: copy only the manifest first so dependency resolution is cached.
COPY pyproject.toml README.md ./

# Initialize a throwaway git repo so hatch-vcs has a tag to read, and pre-resolve
# project metadata. The pretend-version env vars above are the real safeguard.
RUN git init -q && git add -A && git -c user.email=dev@example.com -c user.name=dev commit -qm init \
    && git tag v0.0.0

# Copy the full source so editable install picks up the package.
COPY . .

# Install the project in editable/development mode with ALL runtime extras plus
# the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
