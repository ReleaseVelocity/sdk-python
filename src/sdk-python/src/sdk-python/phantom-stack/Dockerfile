FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context is not a git
# repo, so we provide a fallback version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# Copy dependency manifest + sources needed to resolve the version/package.
# (hatchling needs the package tree present for an editable install.)
COPY pyproject.toml README.md ./
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies and the full dev/test toolchain.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
