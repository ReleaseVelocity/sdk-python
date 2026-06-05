FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include git history/tags, provide a fallback version so the build succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# Minimal build tooling. Most deps ship wheels; git is useful for tooling/pre-commit.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Layer caching: copy dependency manifest + readme (needed by build backend) first.
COPY pyproject.toml README.md ./

# Pre-create package dir so an editable metadata build can resolve packages.
# (Full source is copied below; this just primes the layer.)
COPY src/strands/__init__.py src/strands/__init__.py

# Install the project in editable/dev mode with all optional + dev dependencies.
# 'all' pulls every model-provider extra; 'dev' pulls test + lint tooling.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Now copy the full source tree (tests, configs, remaining source).
COPY . .

# Re-run install to ensure editable metadata reflects full tree (cheap; deps cached).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
