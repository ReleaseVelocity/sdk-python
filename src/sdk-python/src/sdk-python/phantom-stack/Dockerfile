FROM python:3.12-slim

# hatch-vcs determines version from git tags; this repo is shipped without .git,
# so provide a pretend version so the editable install can build metadata.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the editable install builds metadata
# and hatch reads packages = ["src/strands"]).
COPY src/strands ./src/strands

# Install the project in editable/development mode with ALL runtime extras
# plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
