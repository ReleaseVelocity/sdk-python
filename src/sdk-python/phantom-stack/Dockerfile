FROM python:3.12-slim

# hatch-vcs/setuptools_scm derives the version from git tags. There is no git
# metadata in the build context, so provide an explicit fallback version.
# This is overridable via build arg / env so different environments can set it.
ARG PKG_VERSION=0.0.0+local
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-resolve/install dependencies. The package itself is installed editable
# in the next layer after the full source is copied.
RUN python -m pip install --upgrade pip

# Copy the full source.
COPY . /app

# Install the project in editable/development mode with all optional features
# plus the dev/test toolchain so tests, linters and type-checkers are available.
RUN python -m pip install -e ".[all,dev]"

CMD ["bash"]
