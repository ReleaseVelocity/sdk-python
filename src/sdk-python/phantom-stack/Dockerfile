FROM python:3.12-slim

# Avoid interactive prompts and keep Python output unbuffered
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1

# hatch-vcs determines the version from git tags. The build context has no
# git history, so provide a pretend version so the build backend succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# Minimal build tooling; most deps ship wheels but keep git for any VCS deps.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest and project metadata first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install build.
COPY src ./src

# Install the project in editable/development mode with all optional
# dependencies and the dev/test tooling.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs, etc.)
COPY . .

CMD ["python", "-c", "import strands; print('strands', strands.__name__, 'import OK')"]
