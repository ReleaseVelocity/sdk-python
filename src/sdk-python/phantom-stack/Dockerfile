FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the source tree is shipped
# without a .git directory, provide a pretend version so the build/install works.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# System deps: git (hatch-vcs probing), and portaudio for the optional bidi
# extra (pyaudio) so the full dependency set can be installed.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed by the editable install / hatchling backend).
COPY src/ ./src/

# Install the project in editable/development mode with ALL feature extras and
# the dev/test tooling so a developer can run tests, linters and type checks.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
