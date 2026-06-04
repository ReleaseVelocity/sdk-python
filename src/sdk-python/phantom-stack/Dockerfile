FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include a .git directory, provide an explicit fallback version so the
# editable install succeeds.
ARG HATCH_VCS_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION}
# hatch-vcs uses setuptools-scm under the hood; this is the variable it reads.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION}

# Some optional/native deps (e.g. pyaudio for bidi) need build tooling and
# portaudio headers. These are only required for the optional `bidi` feature
# set, which is excluded from the default test suite, so we avoid installing
# them to keep the build self-contained (no apt network access required).

WORKDIR /app

# Leverage layer caching: copy dependency manifest first, then install.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install).
COPY src/ ./src/

# Install the project in development/editable mode with ALL optional features
# and the full dev toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy the remainder of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
