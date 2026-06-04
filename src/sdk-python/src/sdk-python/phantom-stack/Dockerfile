FROM python:3.12-slim

# hatch-vcs derives the package version from git metadata, so git is required
# at install time. build-essential is included only as a fallback for any
# dependency that needs to compile a native wheel.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install + version detection).
COPY src ./src

# The project uses hatch-vcs (version from git tags). There is no .git in the
# build context, so initialize a throwaway repo with a tag to provide a valid
# version to hatchling during the editable install.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -q -m "build snapshot" && \
    git tag v1.0.0

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

# Re-create git metadata after copying full source so editable version stays valid.
RUN rm -rf .git && \
    git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -q -m "build snapshot" && \
    git tag v1.0.0

CMD ["sleep", "infinity"]
