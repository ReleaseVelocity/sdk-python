FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the source is shipped
# without git metadata, provide a pretend version so the build succeeds.
ARG PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PRETEND_VERSION} \
    HATCH_VCS_PRETEND_VERSION=${PRETEND_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the rest of the source (needed for editable install of src/strands).
COPY . .

# Install the project in editable/development mode with ALL optional features
# plus the dev/test tooling so a developer can run tests, linters, type checks.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
