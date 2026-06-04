FROM python:3.12-slim

# hatch-vcs derives the version from git tags (via setuptools-scm). The source
# tree has no .git directory, so we initialize a throwaway git repo with a tag
# below so the build backend can determine a version natively.
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0.dev0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0.dev0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# portaudio is required to build/run the optional pyaudio (bidi) dependency,
# git is handy for dev workflows. Keep the layer minimal.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install).
COPY src ./src

# Initialize a throwaway git repo with a version tag so hatch-vcs/setuptools-scm
# can detect a version (the source tree ships without a .git directory).
RUN git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -q -m "build snapshot" \
    && git tag v0.0.0

# Install the project in editable/dev mode with all extras + dev tooling.
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
