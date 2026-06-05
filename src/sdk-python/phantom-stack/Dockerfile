FROM python:3.12-slim

# hatch-vcs derives the version from git tags. This source tree has no git
# history/tags, so provide a pretend version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so editable install metadata resolves; real
# source is copied next.
COPY src ./src

# Install the project in editable/development mode with ALL runtime extras
# plus the dev/test tooling.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, etc.)
COPY . .

CMD ["sleep", "infinity"]
