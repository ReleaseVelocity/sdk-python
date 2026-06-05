FROM python:3.12-slim

# hatch-vcs/setuptools-scm normally derives the version from git tags. The
# source tree here is not a git checkout, so provide an explicit version to
# avoid build failures.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so an editable install can resolve the package.
COPY src ./src

# Install the project in editable/development mode with ALL optional feature
# groups plus the dev/test tooling so tests, linters and type checkers are
# all available inside the container.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs, etc.).
COPY . .

CMD ["sleep", "infinity"]
