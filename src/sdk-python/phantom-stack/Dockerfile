FROM python:3.12-slim

# hatch-vcs/setuptools_scm needs a version; there are no git tags in this
# build context, so provide a pretend version for the editable install.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is used by hatch-vcs to derive the version; build-essential is only
# pulled in as a safety net for any sdist that needs compilation.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Bring in the package source needed for the editable (dev-mode) install.
COPY src/strands ./src/strands

# Install the project in editable/dev mode with all model providers plus the
# dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
