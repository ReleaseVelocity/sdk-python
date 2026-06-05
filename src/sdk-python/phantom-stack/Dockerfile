FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context may not be a
# git repo, so provide a fallback version via this env var.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# git is referenced by hatch-vcs / pre-commit tooling
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy source (needed for editable install of the package)
COPY src ./src

# Install the project in editable/development mode with ALL optional deps + dev tools
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy remaining project files (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
