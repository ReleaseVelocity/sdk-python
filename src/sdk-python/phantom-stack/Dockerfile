FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no git repo in the build
# context, so provide a pretend version that hatch-vcs / setuptools-scm honor.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Minimal build tooling; most wheels are prebuilt but a few deps may compile.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the source package (needed because the project installs in editable mode
# and the build backend reads src/strands).
COPY src ./src

# Install the project in editable/development mode with ALL runtime extras and
# the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
