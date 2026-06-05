FROM python:3.12-slim

# Avoid interactive prompts and ensure clean python output
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# Build tools and git are required:
#  - git: hatch-vcs derives the package version from git tags/history
#  - build-essential: some optional deps (e.g. audio/native wheels) may compile
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# --- Dependency layer (better caching) ---
# Copy only the project metadata first so dependency resolution is cached.
COPY pyproject.toml README.md ./

# hatch-vcs requires a git repo with a tag to compute the version.
# Initialize a throwaway repo with a sane version tag so `pip install -e`
# can build/install the package metadata without a real VCS history.
RUN git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -q -m "bootstrap" \
    && git tag v0.0.0

# Copy the actual source so the editable install has the package available.
COPY src ./src

# Install the project in editable/development mode with ALL extras + dev tools.
# This pulls in every model provider, a2a, otel, docs, plus pytest/ruff/mypy/moto.
RUN pip install -e ".[dev,all]"

# Copy the remainder of the project (tests, configs, docs).
COPY . .

# Re-sync git so the full tree is tracked (keeps hatch-vcs happy on rebuilds).
RUN git add -A && git commit -q -m "full tree" || true

CMD ["bash"]
