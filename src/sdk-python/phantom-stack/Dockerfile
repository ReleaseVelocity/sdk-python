FROM python:3.12-slim

# hatch-vcs determines the version from git tags. Since the source tree may not
# contain a .git directory, provide a pretend version so the build succeeds.
ARG PACKAGE_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PACKAGE_VERSION}
ENV HATCH_VCS_PRETEND_VERSION=${PACKAGE_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# Build/runtime libs that some optional dependencies need to compile native code.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install dependencies first (without project source) to leverage caching.
# We do a best-effort install of the heavy optional deps here; the editable
# install below wires up the actual package.
RUN pip install --upgrade pip

# Copy the rest of the project source.
COPY . .

# Install the project in editable/development mode with all optional features
# and the dev/test toolchain.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
