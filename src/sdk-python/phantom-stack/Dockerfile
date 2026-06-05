FROM python:3.12-slim

# hatch-vcs (setuptools_scm) derives the version from git tags. The build
# context has no .git directory, so provide a pretend version to satisfy it.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install metadata resolves.
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the full source tree (overwrites the placeholder package).
COPY . .

CMD ["sleep", "infinity"]
