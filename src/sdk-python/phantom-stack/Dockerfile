FROM python:3.12-slim

# hatch-vcs needs git to determine the version from tags
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the rest of the source (including .git so hatch-vcs can resolve version)
COPY . .

# hatch-vcs derives the version from git tags; ensure a fallback version exists
# so editable install succeeds even without tags in the build context.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# Install the project in editable/development mode with all optional + dev deps.
# "all" pulls every model-provider extra; "dev" pulls test + lint tooling.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
