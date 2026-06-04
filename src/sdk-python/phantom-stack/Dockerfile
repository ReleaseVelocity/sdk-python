FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree provided to us
# is not a git checkout, so we provide an explicit fallback version that
# hatch-vcs (setuptools_scm compatible) will honor.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0
ENV PIP_NO_CACHE_DIR=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so an editable install can resolve metadata
# before the full source is copied.
COPY src ./src

# Install the project in editable/development mode with all optional model
# providers ("all") plus the development & test tooling ("dev"). This mirrors
# the project's hatch test/default environments which use features=["all"].
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
