FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no .git in the build
# context, so provide a pretend version so the build/install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifests first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install.
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain (pytest, ruff, mypy, moto, ...).
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
