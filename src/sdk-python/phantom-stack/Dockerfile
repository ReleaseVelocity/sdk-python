FROM python:3.12-slim

# hatch-vcs derives the package version from git tags, so git is required at build time.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the rest of the source.
COPY . /app

# hatch-vcs needs a git repo with a tag to compute the version.
# Initialize a throwaway repo with a version tag if one is not already present.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -q -m "containerize" && \
    git tag v1.0.0

# Install the project in editable/development mode with ALL extras and dev/test tooling.
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
