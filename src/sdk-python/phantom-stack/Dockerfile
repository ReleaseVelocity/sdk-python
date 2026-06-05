FROM python:3.12-slim

# hatch-vcs (setuptools_scm) needs a version; no .git is present in the build context,
# so provide a pretend version so the build backend can determine it.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is needed by hatch-vcs; gcc for any optional native builds.
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for editable install.
COPY src ./src

# Install the project in editable/development mode with ALL runtime extras plus dev tooling.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
