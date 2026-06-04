FROM python:3.13-slim

# hatch-vcs/setuptools-scm derives the version from git tags. Since the build
# context may not contain git history, provide a pretend version so the
# editable install of the package succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install.
COPY src ./src

# Install the project in editable/development mode with all optional features
# plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
