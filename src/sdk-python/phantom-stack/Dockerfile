FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There's no .git in the build
# context, so provide a pretend version (honored by hatch-vcs/setuptools-scm).
ARG PKG_VERSION=1.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION}
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# Minimal build tooling. Some transitive deps (e.g. audio/native bits used by
# optional extras) may need compilers; install a small toolchain to be safe.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install the project in editable/development mode with ALL runtime extras and
# the dev/test toolchain so a developer can run tests and linters in-container.
COPY src ./src
RUN pip install --no-cache-dir -e ".[all,dev]"

# Now bring in the rest of the source (tests, configs, etc.)
COPY . .

CMD ["sleep", "infinity"]
