FROM python:3.13-slim

# hatch-vcs derives the package version from git tags. The source tree has no
# .git directory, so we provide a pretend version to the build backend.
ENV HATCH_BUILD_HOOKS_ENABLE=0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# git is required only so hatch-vcs has a clean repo to read a tag from.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the full source tree.
COPY . .

# hatch-vcs reads the version from git tags. Initialize a repo with a tag so
# the editable install can resolve a concrete version.
RUN if [ ! -d .git ]; then \
        git init -q \
        && git config user.email "dev@local" \
        && git config user.name "dev" \
        && git add -A \
        && git commit -qm "phantom-stack build snapshot" \
        && git tag v0.0.0 ; \
    fi

# Install the SDK in editable/development mode with ALL runtime extras plus the
# dev/test toolchain (pytest, moto, ruff, mypy, hatch, ...).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
