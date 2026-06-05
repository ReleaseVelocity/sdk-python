FROM python:3.12-slim

# System deps: git is needed because hatch-vcs derives the version from git tags.
# build-essential is included in case any optional dependency needs to compile.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

RUN pip install --no-cache-dir --upgrade pip

# Copy the full source tree.
COPY . .

# hatch-vcs derives the package version from git tags, so initialize a repo
# with a version tag, then install the project in editable/development mode
# with ALL optional features plus the dev/test toolchain (pytest, moto, ruff,
# mypy, etc.).
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "bootstrap" && \
    git tag v0.0.0 && \
    pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
