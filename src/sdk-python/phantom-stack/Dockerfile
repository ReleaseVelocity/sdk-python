FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no .git dir in this
# build context, so provide a pretend version so the editable install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0.dev0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for the editable install / metadata build).
COPY src ./src

# Install the project in editable/development mode with ALL runtime extras
# plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
# Note: the `bidi` extra (pyaudio) is intentionally not part of `[all]` and
# its tests are ignored by the project's pytest addopts.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
