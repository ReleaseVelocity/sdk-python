FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no git repo in this
# context, so provide a pretend version (read by setuptools-scm/hatch-vcs).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Build dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so the editable install has something to build.
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
