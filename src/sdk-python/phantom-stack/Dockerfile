FROM python:3.12-slim

# System deps: git (hatch-vcs version detection fallback), build tools for any
# native wheels (e.g. pyaudio), and portaudio for the optional bidi extras.
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    build-essential \
    portaudio19-dev \
    curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git tags. The source is shipped without a
# .git directory, so provide an explicit version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so the editable install has something to link.
COPY src ./src

# Install the project in editable/dev mode with ALL runtime extras plus the
# dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in tests and the rest of the project.
COPY . .

CMD ["bash"]
