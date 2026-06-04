FROM python:3.12-slim

# hatch-vcs determines version from git tags. Source is copied without .git,
# so provide a pretend version (read by hatch-vcs / setuptools_scm).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create package dir so editable install metadata resolves.
COPY src ./src

# Install the project in editable/dev mode with all + dev dependencies.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, etc.)
COPY . .

CMD ["sleep", "infinity"]
