FROM python:3.12-slim

# hatch-vcs determines version from git tags; no .git is shipped here, so we
# provide a pretend version that hatch-vcs/setuptools-scm honor.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so editable metadata build can resolve packages.
RUN mkdir -p src/strands

# Install the project in editable/dev mode with ALL optional dependencies
# plus the dev/test toolchain (pytest, moto, mypy, ruff, etc.).
# We do a metadata-only resolve here against the manifest; full source is
# copied in the next layer.
COPY src ./src
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
