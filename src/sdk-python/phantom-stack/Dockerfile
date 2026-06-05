FROM python:3.13-slim

# hatch-vcs derives the version from git tags via setuptools-scm. The build
# context has no .git, so provide a pretend version to satisfy the dynamic
# version build step.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+dev \
    HATCH_VCS_PRETEND_VERSION=0.0.0+dev
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install / version resolution).
COPY src ./src

# Install the project in editable/development mode with ALL extras + dev/test deps.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
