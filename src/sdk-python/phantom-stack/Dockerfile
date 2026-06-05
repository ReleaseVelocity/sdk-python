FROM python:3.13-slim

# hatch-vcs derives the version from git tags. This source tree has no .git,
# so provide a pretend version for the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+dev \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source (needed for editable install of the package).
COPY src ./src

# Install the project in editable/development mode with all optional features
# plus dev/test tooling. `bidi`/`bidi-all` extras need native portaudio and are
# intentionally excluded (not part of the `all` extra either).
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
