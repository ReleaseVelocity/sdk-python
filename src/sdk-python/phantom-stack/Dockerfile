FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1

# git is required because the project derives its version from git tags (hatch-vcs)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the full source (needed for editable install and git metadata)
COPY . .

# hatch-vcs needs a git repo with at least one tag to determine the version.
# Initialize a repo and create a baseline tag if one is not present.
RUN if [ ! -d .git ]; then git init -q && git config user.email "dev@example.com" && git config user.name "dev"; fi \
    && git add -A 2>/dev/null || true \
    && git commit -qm "container baseline" 2>/dev/null || true \
    && git tag v0.0.0 2>/dev/null || true

# Install the project in editable/development mode with all optional + dev dependencies
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
