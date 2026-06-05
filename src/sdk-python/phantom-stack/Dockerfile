FROM python:3.13-slim

# hatch-vcs derives the version from git tags. Since the build context has no
# git history, provide a pretend version so the editable install succeeds.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# The strands package needs to exist for editable install resolution.
COPY src/ ./src/

# Install the project in editable/development mode with ALL runtime extras
# plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remainder of the project (tests, configs, docs).
COPY . .

# Sanity: ensure the package imports.
RUN python -c "import strands; print('strands import OK')"

CMD ["sleep", "infinity"]
