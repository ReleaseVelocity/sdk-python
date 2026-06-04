FROM python:3.12-slim

# System deps: git is required by hatch-vcs for version detection,
# build tools for any native wheels (e.g. pyaudio is excluded from 'all').
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy project manifest and metadata first for better layer caching.
COPY pyproject.toml README.md ./

# hatch-vcs needs git history to compute a version. Provide a fallback
# version so the editable install succeeds without git tags.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_BUILD_NO_HOOKS=false

# Copy the source so the editable install can resolve the package.
COPY src ./src

# Initialize a git repo with a tag so hatch-vcs can resolve a version.
RUN git init -q && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A && git commit -qm "init" \
    && git tag v0.0.0

# Install the package in editable/dev mode with all model providers plus
# the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs).
COPY . .

CMD ["sleep", "infinity"]
