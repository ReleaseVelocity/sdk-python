FROM python:3.13-slim

# hatch-vcs reads the version from git tags; ensure git is available and a
# fallback version is provided in case the build context has no git metadata.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

# git is needed by hatch-vcs for version detection during editable install.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the rest of the source so editable install resolves the package tree.
COPY . .

# Initialize a git repo with a tag so hatch-vcs can derive a version, in case
# the source was copied without .git metadata.
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -qm "containerize" && \
        git tag v0.0.0; \
    fi

# Install the project in editable/development mode with ALL optional
# dependencies plus dev/test tooling so a developer can run tests and linters.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
