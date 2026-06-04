FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Allow overriding without needing
# real git history so the editable install succeeds in any environment.
ARG PKG_VERSION=0.1.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is required by hatch-vcs to resolve the version from tags.
RUN apt-get update && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Bring in the source tree.
COPY src ./src

# Initialize a git repo with a tag so hatch-vcs can determine a version,
# then install the project in editable/development mode with ALL extras
# plus the dev/test toolchain.
RUN git init -q && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A && git commit -qm "init" \
    && git tag "v${PKG_VERSION}" \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
