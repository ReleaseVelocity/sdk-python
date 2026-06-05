FROM python:3.13-slim

# hatch-vcs derives the version from git tags. There is no .git in the build
# context, so provide a pretend version to satisfy the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Install dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the full source tree (needed for editable install of the package).
COPY . .

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
