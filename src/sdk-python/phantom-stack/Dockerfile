FROM python:3.12-slim

# hatch-vcs determines the version from git tags. Since the source is copied
# without a git repository, provide a pretend version so the build backend
# does not fail when installing in editable mode.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first to leverage Docker layer caching.
COPY pyproject.toml README.md ./

# Create the package source path so editable install can resolve the package
# metadata during the dependency-resolution layer.
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install the project with ALL optional features plus dev/test dependencies.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Now copy the full project source (overrides the placeholder package).
COPY . .

# Reinstall editable to ensure metadata/entry points reflect the full source.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
