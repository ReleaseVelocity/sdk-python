FROM python:3.12-slim

# hatch-vcs determines the version from git tags. Since the build context
# may not contain git history, provide a pretend version so the build
# backend can resolve a version without failing.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency/build manifests first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-resolve the package metadata layer. We copy the source tree afterwards,
# but installing build tooling first keeps rebuilds fast.
RUN pip install --upgrade pip

# Copy the full project source.
COPY . /app

# Install the project in editable/development mode with ALL optional features
# plus the development & test tooling so a developer can run tests and linters.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
