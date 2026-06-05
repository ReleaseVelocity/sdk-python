FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no git
# history, so provide a pretend version for setuptools_scm (used by hatch-vcs).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

WORKDIR /app

# git is required by hatch-vcs/setuptools_scm version detection at build time.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so editable install resolves the package.
COPY src ./src

# Initialize a throwaway git repo with a tag so hatch-vcs can determine a version,
# then install the project in editable/development mode with ALL extras + dev/test tools.
RUN git init -q . \
    && git config user.email "build@local" \
    && git config user.name "build" \
    && git add -A \
    && git commit -qm "build" \
    && git tag v0.0.0 \
    && pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
