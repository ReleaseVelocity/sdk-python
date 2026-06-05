FROM python:3.11-slim

# hatch-vcs derives the version from git tags. The build context has no .git
# directory, so provide an explicit version to setuptools_scm/hatch-vcs.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Build deps occasionally needed for native wheels (e.g. pyaudio is not in our
# extras, so keep this minimal). git is handy for hatch tooling.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

# Leverage layer caching: copy only the manifest first.
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install.
COPY src ./src

# Install the project in editable/development mode with all runtime extras plus
# dev/test tooling so a developer can run pytest, ruff, mypy, hatch, etc.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["python", "-c", "import strands; print('strands import OK')"]
