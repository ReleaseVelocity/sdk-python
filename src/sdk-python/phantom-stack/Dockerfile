FROM python:3.12-slim

ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    # The project uses hatch-vcs (git tags) for versioning. No .git is present in
    # the build context, so provide a pretend version that setuptools_scm/hatch-vcs honor.
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy source needed for the editable install
COPY src ./src

# Install the project in editable/development mode with all extras + dev tools.
# "all" pulls every model provider; "dev" pulls pytest, ruff, mypy, moto, etc.
RUN pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
