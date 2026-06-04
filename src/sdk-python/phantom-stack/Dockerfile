FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# contain git history/tags, provide a pretend version so the build succeeds.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# Minimal build/runtime tooling. git is useful for dev workflows; build-essential
# is only added in case a transitive dependency needs to compile from source.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Install the development workflow tooling used by this project (hatch + uv).
RUN pip install hatch uv

# Layer caching: copy only the dependency manifest first.
COPY pyproject.toml README.md ./

# Pre-resolve dependencies against the manifest so editable install is fast even
# if source changes. We install the package itself afterwards once sources exist.
# (The package build needs src/, so the real install happens after the COPY below.)

# Copy the full project source.
COPY . .

# Install the project in editable/development mode with ALL optional model
# providers and the full dev/test toolchain (pytest, moto, mypy, ruff, etc.).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
