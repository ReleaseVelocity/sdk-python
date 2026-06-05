FROM python:3.12-slim

# Avoid interactive prompts and ensure unbuffered output
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs derives the version from git tags. Since the source is shipped
# without a .git directory, tell setuptools_scm (used by hatch-vcs) to use a
# fixed fallback version so the build does not fail.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

WORKDIR /app

# Copy dependency manifest first to leverage Docker layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so editable install can resolve the package.
COPY src ./src

# Install the project in editable/development mode with ALL runtime extras
# (model providers, a2a, otel, docs, ...) plus the dev/test tooling.
# The "bidi" extra is intentionally excluded because it requires the native
# PortAudio library (pyaudio) and its tests are excluded by the project config.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
