FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs derives the version from git tags. Provide a pretend version so the
# build does not require a git history.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-resolve dependencies against the source tree so the editable install is fast.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the project in editable/dev mode with all optional + dev dependencies.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the rest of the project.
COPY . .

CMD ["sleep", "infinity"]
