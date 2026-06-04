FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the source is shipped
# without a .git directory, provide a pretend version so the build succeeds.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Source layout required for the editable install (hatch wheel target = src/strands).
COPY src/strands ./src/strands

# Install the project in editable/development mode with ALL optional providers
# and the full dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install -e ".[all,dev]"

# Bring in the test suites and remaining project files.
COPY . .

CMD ["sleep", "infinity"]
