FROM python:3.12-slim

# hatch-vcs derives the version from git tags. This source tree has no .git,
# so provide a pretend version that hatch-vcs/setuptools-scm will honor.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Install dependency manifest first to leverage layer caching.
# pyproject.toml drives the editable install with all extras + dev tools.
COPY pyproject.toml README.md ./

# Pre-fetch dependencies using the manifest. We copy the source afterward,
# but installing now warms the layer cache for unchanged manifests.
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies and the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
