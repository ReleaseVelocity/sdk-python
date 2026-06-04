FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Source is copied without a .git
# directory, so provide a pretend version for the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+docker \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+docker \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Build tooling: some transitive deps (e.g. pyaudio is excluded here, but a2a/
# others) may need compilation. Keep it minimal.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the build is an editable install of
# the local project, and the version backend reads the tree).
COPY src ./src

# Install the project in editable/development mode with ALL optional and dev
# dependencies so tests, linters and every provider can be exercised.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
