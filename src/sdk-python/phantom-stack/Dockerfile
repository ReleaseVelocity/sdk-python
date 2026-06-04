FROM python:3.13-slim

# hatch-vcs derives the package version from git tags. The build context may
# have no .git metadata, so provide an explicit pretend version to keep the
# build backend happy. Overridable via build arg / env var.
ARG HATCH_VCS_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION}
# hatch-vcs uses setuptools-scm under the hood; this env var is the one it
# actually honours for a pinned version when no git metadata is present.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# git: used by hatch-vcs and general dev workflows.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

RUN pip install --upgrade pip

# Copy the full project. This is a small pure-Python SDK, so a single copy
# keeps the editable install simple and correct.
COPY . .

# Install in editable/development mode with every optional model-provider extra
# ("all") plus the dev tooling ("dev": pytest, pytest-asyncio, pytest-xdist,
# pytest-cov, moto, ruff, mypy, etc.) so a developer can run tests and linters.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
