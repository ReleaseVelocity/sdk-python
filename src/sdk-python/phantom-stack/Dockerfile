FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# git is required by hatch-vcs to derive the package version from tags.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# hatch-vcs needs a git repo with a tag to compute the version.
# Initialize a throwaway repo with a baseline tag so the build is deterministic
# even when the source tree is not a git checkout.
RUN git init -q . \
    && git config user.email "dev@local" \
    && git config user.name "dev" \
    && git add pyproject.toml README.md \
    && git commit -qm "bootstrap" \
    && git tag v0.0.0

# Bring in the full source so the editable install picks up the package.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the project in editable/development mode with ALL runtime extras
# plus the dev/test toolchain (pytest, moto, ruff, mypy, ...).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
