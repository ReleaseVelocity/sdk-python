FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There's no git history in the
# container build context, so provide a pretend version for the build backend.
ARG PKG_VERSION=0.0.0+dev
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    HATCH_VCS_PRETEND_VERSION=${PKG_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# The wheel package dir must exist for an editable install to resolve.
COPY src ./src

# Install the project in editable/development mode with all optional
# dependencies plus dev/test tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
