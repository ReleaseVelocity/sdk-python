FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include git history, provide a pretend version so the editable install works.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# Build deps: git for any VCS-based metadata, plus build-essential for native wheels
# that may not ship prebuilt for slim (e.g. some transitive deps).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest + sources needed for an editable install.
# hatchling needs the package source present at install time.
COPY pyproject.toml README.md ./
COPY src/strands ./src/strands

# Install the project in editable/development mode with ALL optional features
# and dev/test tooling so a developer can run tests, linters, and type checks.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs) for development.
COPY . .

CMD ["sleep", "infinity"]
