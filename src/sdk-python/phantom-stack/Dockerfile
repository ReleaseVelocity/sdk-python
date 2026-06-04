FROM python:3.12-slim

# Allow hatch-vcs to derive a version without requiring real git tags.
ARG STRANDS_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# git is required by hatch-vcs (version source = vcs); build-essential kept out
# unless native compilation is actually needed.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install of the project).
COPY src ./src

# Initialize a throwaway git repo with a tag so hatch-vcs can resolve a version.
RUN git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -q -m "containerize" \
    && git tag "v${STRANDS_VERSION}"

# Install the project in editable/dev mode with ALL dependencies + dev tools.
RUN pip install -e ".[dev,all]"

# Bring in the rest of the project (tests, configs, etc.).
COPY . .

CMD ["sleep", "infinity"]
