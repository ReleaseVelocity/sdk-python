FROM python:3.13-slim

WORKDIR /app

ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# hatch-vcs derives the version from git tags. The build context has no .git
# metadata, so provide an explicit fallback version for this package.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the source tree and tests
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the project in editable/development mode with ALL optional features
# plus the dev/test tooling (pytest, ruff, mypy, moto, etc.) so a developer can
# run tests, linters and type checks inside the container.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
