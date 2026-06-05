FROM python:3.12-slim

# hatch-vcs (setuptools_scm) derives the version from git tags. The build
# context has no .git, so provide a pretend version to allow editable install.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# Minimal build deps: pyaudio (bidi extra) needs portaudio headers; git kept
# for tooling. Try to keep this lean.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# The wheel package lives under src/strands; create a stub so the editable
# install can resolve the package path before full source is copied.
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install the project in editable/development mode with ALL extras plus the
# dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Now copy the full source tree (overwrites the stub).
COPY . .

CMD ["sleep", "infinity"]
