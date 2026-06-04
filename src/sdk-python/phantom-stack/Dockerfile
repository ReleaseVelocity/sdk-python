FROM python:3.13-slim

# hatch-vcs derives the version from git tags. There is no git repo in this
# build context, so provide a pretend version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Upgrade pip tooling. hatchling/hatch-vcs are the build backends.
RUN pip install --no-cache-dir --upgrade pip setuptools wheel

# Copy the full source tree (needed for editable install of the package).
COPY . .

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
