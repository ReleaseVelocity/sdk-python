FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1

# hatch-vcs derives the version from git tags. The source tree shipped here
# has no .git directory, so we provide a pretend version that hatch-vcs honors.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

WORKDIR /app

# Install dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-install build backend so editable install of the full project + dev/all
# extras resolves quickly. We do the real install after copying the source.
RUN pip install --upgrade pip hatchling hatch-vcs

# Copy the full project source.
COPY . /app

# Install the project in editable/development mode with ALL optional deps and
# the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
