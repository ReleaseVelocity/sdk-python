FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Provide a pretend version so the
# build succeeds without git history available in the build context.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is needed by hatch-vcs / pre-commit tooling; build-essential covers any
# packages that need native compilation (e.g. pyaudio for the bidi extra is not
# installed here, but other transitive deps may build wheels).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for layer caching, plus the sources required by
# the build backend (hatchling reads packages = ["src/strands"]).
COPY pyproject.toml README.md ./
COPY src ./src

# Install the project in editable/development mode with ALL extras plus the dev
# tooling (pytest, ruff, mypy, moto, etc.) so a developer can run tests & linters.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
