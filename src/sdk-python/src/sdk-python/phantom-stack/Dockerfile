FROM python:3.13-slim

# hatch-vcs derives the package version from git tags. The build context is not a
# git repository, so we provide an explicit fallback version that hatch-vcs honors.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# Minimal build tooling. Most wheels are prebuilt; build-essential is kept lean
# in case a source-only dependency needs compiling.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install.
COPY src ./src

# Editable install with all optional feature extras plus the dev/test toolchain.
RUN pip install -e ".[all]" \
    && pip install \
        "pytest>=9.0.0,<10.0.0" \
        "pytest-cov>=7.0.0,<8.0.0" \
        "pytest-asyncio>=1.0.0,<1.4.0" \
        "pytest-xdist>=3.0.0,<4.0.0" \
        "moto>=5.1.0,<6.0.0" \
        "mypy>=1.15.0,<2.0.0" \
        "ruff>=0.13.0,<0.15.0" \
        "hatch>=1.0.0,<2.0.0" \
        "commitizen>=4.4.0,<5.0.0" \
        "pre-commit>=3.2.0,<4.6.0"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
