FROM python:3.12-slim

# hatch-vcs derives the version from git tags. This repo snapshot has no .git,
# so provide a pretend version to satisfy the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# portaudio is needed to build pyaudio (pulled in by the optional bidi feature).
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        gcc \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the build backend reads packages = ["src/strands"]).
COPY src ./src

# Install in editable/development mode with all + dev dependencies so a developer
# can run the full test suite, linters and type checks inside the container.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
