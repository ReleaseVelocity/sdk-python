FROM python:3.12-slim

# hatch-vcs/setuptools_scm derives the version from git tags. The build context
# has no .git, so provide a pretend version to allow the editable install.
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    HATCH_VCS_PRETEND_VERSION=${PKG_VERSION} \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because we install in editable mode and the
# build backend reads packages from src/strands).
COPY src ./src

# Install the project in editable/development mode with all optional features
# and dev/test tooling. The bidi extra is intentionally excluded: it requires
# system audio libraries (portaudio/pyaudio) and the project's default test
# config ignores bidi tests.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, etc.).
COPY . .

CMD ["sleep", "infinity"]
