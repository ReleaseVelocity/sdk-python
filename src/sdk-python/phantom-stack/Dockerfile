FROM python:3.12-slim

# Allow hatch-vcs to resolve a version without git history
ARG PROJECT_VERSION=0.1.0.dev0
ENV HATCH_VCS_PRETEND_VERSION=${PROJECT_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION=${PROJECT_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# Note: the "all" extras group is pure-Python; the optional "bidi" group
# (which needs portaudio system libs) is intentionally excluded and the
# project's default test config ignores the bidi test suite.

WORKDIR /app

# Copy dependency manifests first for better layer caching
COPY pyproject.toml README.md ./

# Pre-create package dir so editable install metadata resolves
COPY src ./src

# Install project in editable/dev mode with all extras + dev/test tooling
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the source tree
COPY . .

CMD ["sleep", "infinity"]
