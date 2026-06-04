FROM python:3.12-slim

# git is required because the project uses hatch-vcs (version from git tags).
# build-essential is needed for any optional deps that compile native extensions
# (e.g. some transitive deps of the "all" feature set).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the rest of the source.
COPY . /app

# hatch-vcs derives the version from git metadata. The source is not a git repo,
# so initialize one with a tag to give the build backend a valid version.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "containerize" && \
    git tag v1.0.0

# Install the project in editable/development mode with dev tooling and all
# optional model-provider features so the full test suite can be collected/run.
RUN pip install --upgrade pip && \
    pip install -e ".[dev,all]"

CMD ["sleep", "infinity"]
