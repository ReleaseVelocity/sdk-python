FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context may not
# include git history/tags, so provide a fallback version for the build.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the project installs in editable mode
# and hatchling reads packages = ["src/strands"]).
COPY src ./src

# Install the project in editable/dev mode with ALL optional deps + dev tools.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
