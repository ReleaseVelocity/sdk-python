FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is required because the project uses hatch-vcs to derive its version
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the rest of the source
COPY . /app

# hatch-vcs needs a git repo with a tag to determine the version.
# The project has no .git in this environment, so create a minimal one.
RUN if [ ! -d /app/.git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -qm "containerize" && \
        git tag v1.0.0 ; \
    fi

# Install the project in editable/development mode with ALL dependencies,
# including dev/test tooling (pytest, ruff, mypy, moto, etc.)
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
