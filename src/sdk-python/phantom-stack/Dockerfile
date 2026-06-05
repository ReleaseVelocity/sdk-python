FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# contain git history, provide a fallback version so the build succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

# git is needed by hatch-vcs at build time; build tools help with any
# native wheels that lack prebuilt distributions.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-install the dependency set. The package itself is installed editable
# below once full source is present.
RUN pip install --upgrade pip

# Copy the full source tree.
COPY . .

# Install the project in editable/development mode with ALL optional
# providers plus the dev/test toolchain (pytest, ruff, mypy, moto, ...).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
