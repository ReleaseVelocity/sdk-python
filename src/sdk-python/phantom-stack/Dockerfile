FROM python:3.12-slim

# hatch-vcs (setuptools_scm) needs a version. No git tags exist in this
# workspace, so provide a pretend version. Overridable at build time.
ARG SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${SETUPTOOLS_SCM_PRETEND_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

RUN pip install --upgrade pip

# Copy the full project source (build context is the project root).
COPY . /app

# Install the project in editable/development mode with all model-provider
# extras plus the dev/test toolchain (pytest, moto, mypy, ruff, etc.).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
