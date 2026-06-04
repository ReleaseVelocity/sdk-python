FROM python:3.12-slim

# git is needed because hatch-vcs derives the version from git tags/metadata
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# Copy dependency manifest first for better layer caching
COPY pyproject.toml ./

# hatch-vcs requires git metadata to compute a version. Provide a fallback so
# an editable install works even without a tagged git checkout.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_BUILD_NO_HOOKS=1

# Copy the rest of the project
COPY . .

# Initialize a git repo with a tag so hatch-vcs can resolve a version,
# then install the project in editable/development mode with all extras + dev tooling.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "dev build" && \
    git tag v0.0.0 && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
