FROM python:3.12-slim

# hatch-vcs derives the package version from git tags. The build context may not
# be a git repository, so provide a fallback version via the setuptools_scm /
# hatch-vcs pretend-version env var. Override with --build-arg SDK_VERSION=...
ARG SDK_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${SDK_VERSION} \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# git is useful for hatch-vcs/pre-commit tooling. build-essential is included
# because some optional deps (e.g. pyaudio in bidi) may need to compile, but we
# don't install bidi here so keep it minimal.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest + readme first for better layer caching. The editable
# install reads pyproject.toml and README.md (readme reference) to build metadata.
COPY pyproject.toml README.md ./

# Copy the full source tree (editable install needs the package source present).
COPY . .

# Install the project in editable/development mode with ALL optional providers
# plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
