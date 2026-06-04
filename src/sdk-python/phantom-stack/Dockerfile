FROM public.ecr.aws/docker/library/python:3.12-slim

# hatch-vcs/setuptools_scm derives the version from git tags. The build context
# has no git metadata, so we provide an explicit fallback version. This can be
# overridden at build time with --build-arg.
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable wheel build can resolve packages.
COPY src ./src

# Install the project in editable/development mode with ALL optional feature
# groups plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
