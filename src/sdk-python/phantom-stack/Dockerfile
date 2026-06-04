FROM python:3.12-slim

# hatch-vcs determines version from git tags; no .git is present in the build
# context, so provide a pretend version (hatch-vcs honors SETUPTOOLS_SCM_PRETEND_VERSION).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source so the editable install can resolve the package.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the project in editable/dev mode with all optional deps + dev/test tooling.
RUN pip install -e ".[dev,all]"

CMD ["sleep", "infinity"]
