FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is needed because the project uses hatch-vcs (version derived from git tags)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# hatch-vcs needs a git repo with at least one tag to resolve the dynamic version.
# Initialize a throwaway repo + tag so the editable install can compute a version.
RUN git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -qm "init" \
    && git tag v0.0.0

# Copy the rest of the source
COPY . .

# Re-commit full source so hatch-vcs sees a clean tree
RUN git add -A && git commit -qm "source" || true

# Install the project in editable/development mode with all extras + dev tooling
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
