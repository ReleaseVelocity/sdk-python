FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Provide a pretend version so the
# editable install does not fail when there is no git history/tags available.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

# portaudio19-dev / gcc are needed only for the optional bidi extra (pyaudio);
# we do not install bidi here, but build tools help any source-only wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# hatchling needs the package source present to build the editable install,
# and a git repo for hatch-vcs to operate against.
COPY src ./src

# Initialize a git repo with a tag so hatch-vcs has something to resolve,
# in addition to the pretend version env var above (belt and suspenders).
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -q -m "init" && \
    git tag v0.0.0

# Install the project in editable/development mode with ALL extras + dev deps.
RUN pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
