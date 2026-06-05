FROM python:3.12-slim

# hatch-vcs determines the version from git tags. Since the source is copied
# without a .git directory, provide a fallback version so the build succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the full source (needed for editable install of the package)
COPY . .

# Install the project in editable/development mode with all optional
# dependencies and the dev toolchain (pytest, ruff, mypy, moto, etc.)
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
