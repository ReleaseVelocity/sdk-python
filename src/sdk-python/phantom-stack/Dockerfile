FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a fallback version via setuptools_scm/hatch-vcs pretend version.
ENV HATCH_BUILD_NO_HOOKS=false \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# pyaudio (bidi extra, optional) and some native deps need build tooling.
# Keep minimal: only git for VCS metadata and basic build essentials.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Copy source so the editable install can resolve packages.
COPY src ./src

# Install the project in editable/dev mode with all optional + dev dependencies.
# "all" pulls every model provider; "dev" pulls test/lint tooling.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
