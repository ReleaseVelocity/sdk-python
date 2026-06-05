FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree has no .git
# directory, so provide a pretend version to allow an editable install.
ARG PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=${PRETEND_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION=${PRETEND_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-install dependencies (without the package itself) to cache the heavy
# layer. We create a minimal package stub so pip can resolve extras.
RUN pip install --no-cache-dir --upgrade pip

# Copy the full source tree.
COPY . .

# Install the project in editable/development mode with ALL optional model
# providers and the dev/test tooling (pytest, moto, ruff, mypy, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
