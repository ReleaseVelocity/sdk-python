FROM python:3.12-slim

# hatch-vcs derives the version from git tags; the source tree here has no
# .git directory, so provide a static version to the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching. README is referenced
# by pyproject (readme = "README.md") so it is required during the build.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install can resolve packages while
# the real source is mounted/copied in the next step.
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install the project in editable/development mode with all runtime extras plus
# the full dev/test toolchain. "all" pulls every model provider; "dev" pulls
# pytest, ruff, mypy, moto, etc.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Now copy the full source tree (overwrites the placeholder package files).
COPY . .

# Re-run the editable install so any newly added entry points/metadata resolve
# against the real source. Dependencies are already cached so this is fast.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
