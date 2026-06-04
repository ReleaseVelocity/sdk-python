FROM python:3.12-slim

# hatch-vcs (setuptools_scm) determines version from git tags. There is no
# .git in the build context, so provide a pretend version to satisfy the build.
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION}
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# Install dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-install dependencies (without the project itself yet) to leverage caching.
# We install the project in editable mode with all + dev extras once source is present.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the project in editable/development mode with all optional and dev deps.
RUN pip install -e ".[all,dev]"

CMD ["python", "-m", "pytest", "--co", "-q"]
