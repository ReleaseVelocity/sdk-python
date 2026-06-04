FROM python:3.11-slim

# hatch-vcs needs git metadata to determine the version. We init a repo and tag
# it inside the build so editable install succeeds without external VCS state.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the rest of the source
COPY . /app

# Initialize a git repo + tag so hatch-vcs can resolve a version,
# then install the project in editable/dev mode with all extras + dev tools.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -q -m "init" && \
    git tag v1.0.0 && \
    pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
