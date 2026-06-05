FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include a populated .git, provide a fallback version so the build/install works.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

WORKDIR /app

# All runtime/dev deps ship as wheels and hatch-vcs version is supplied via
# SETUPTOOLS_SCM_PRETEND_VERSION, so no system packages are required.
RUN pip install --upgrade pip

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so an editable install can resolve the package.
COPY src ./src

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
