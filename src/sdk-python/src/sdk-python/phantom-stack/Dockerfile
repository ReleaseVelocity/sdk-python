FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree here has no
# .git directory, so provide a pretend version to allow the build to succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-install dependencies (without the project itself) to leverage caching.
# We install the full set of optional + dev dependencies.
RUN pip install --upgrade pip && \
    pip install hatchling hatch-vcs

# Copy the full source tree.
COPY . .

# Install the project in editable/development mode with ALL extras and dev deps.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
