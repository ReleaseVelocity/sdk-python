FROM python:3.12-slim

WORKDIR /app

# hatch-vcs derives the version from git tags. The build context has no git
# history, so provide a pretend version to let the build/install succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Bring in the package source so the editable install can resolve packages.
COPY src ./src

# Install the project in editable/development mode with ALL extras + dev tools.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
