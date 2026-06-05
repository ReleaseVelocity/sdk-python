FROM python:3.12-slim

# hatch-vcs determines the version from git tags. Provide an explicit version
# via SETUPTOOLS_SCM_PRETEND_VERSION so the build needs neither git nor network.
ARG PACKAGE_VERSION=0.1.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PACKAGE_VERSION} \
    HATCH_VCS_PRETEND_VERSION=${PACKAGE_VERSION} \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the full source tree.
COPY . .

# Install the project in editable/development mode with all extras + dev tools.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
