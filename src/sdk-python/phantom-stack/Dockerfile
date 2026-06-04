FROM python:3.12-slim

# hatch-vcs/setuptools-scm needs a version; project has no git history in container.
# This can be overridden at build time.
ARG PROJECT_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PROJECT_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# Minimal system deps. portaudio is needed only for the optional bidi extra (pyaudio);
# we do not install bidi-all, so keep base lean. git installed so any vcs tooling works.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Layer caching: copy only the manifest first.
COPY pyproject.toml README.md ./

# Install all runtime + optional + dev dependencies. The project itself is installed
# editable later once full source is copied; here we warm the dependency layer.
RUN pip install --upgrade pip && \
    pip install hatchling hatch-vcs

# Copy full source.
COPY . .

# Install the project in editable/development mode with all optional and dev extras.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
