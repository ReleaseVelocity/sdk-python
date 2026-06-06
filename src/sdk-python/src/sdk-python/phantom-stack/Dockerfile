FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context has no
# git history, provide a pretend version so the editable install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Minimal build tooling. Most deps ship wheels; gcc is kept available in case a
# transitive dependency needs to compile a small extension.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy the dependency manifest first to leverage Docker layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install metadata step resolves.
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install all optional feature sets plus dev/test tooling in editable mode.
# The "all" extra pulls in every model provider; "dev" adds pytest, ruff, mypy,
# moto, etc. (bidi extras are intentionally excluded as their tests are skipped
# and pyaudio needs system audio libraries).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Now copy the full source tree (overwrites the placeholder package).
COPY . .

CMD ["bash", "-lc", "python -m pytest --co -q && echo 'COLLECTION OK' && tail -f /dev/null"]
