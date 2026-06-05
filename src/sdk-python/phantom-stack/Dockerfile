FROM python:3.12-slim

# hatch-vcs/setuptools_scm needs a version since .git may be absent
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# System deps: git for any vcs operations, build tools only if needed by wheels.
# pyaudio (bidi extra) needs portaudio, but we install [all,dev] which excludes bidi.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy source (needed for editable install / hatchling build target)
COPY src ./src

# Install project in editable/dev mode with all optional deps + dev tooling
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy remaining project files (tests, configs)
COPY . .

CMD ["sleep", "infinity"]
