FROM python:3.12-slim

# Tell hatch-vcs the version since .git history may be shallow/absent
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install)
COPY src ./src

# Install the project in editable/development mode with all extras + dev/test deps.
# "all" pulls in every model provider so the full test suite can be collected.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, etc.)
COPY . .

CMD ["sleep", "infinity"]
