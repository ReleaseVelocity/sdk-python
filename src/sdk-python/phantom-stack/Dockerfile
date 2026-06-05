FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context has no
# .git, provide a pretend version so the build backend succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# portaudio is required to build pyaudio (pulled in by the bidi extra).
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    git \
    portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source needed for editable install metadata resolution.
COPY src/strands ./src/strands

# Install the project in editable/development mode with ALL optional deps,
# the dev/test tooling, and the bidi stack.
RUN pip install --no-cache-dir -e ".[all,dev,bidi-all]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
