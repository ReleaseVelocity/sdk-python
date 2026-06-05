FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree may not be a git
# checkout (or have no tags), so provide an explicit fallback version.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Minimal system deps. portaudio is needed by pyaudio (pulled in by the bidi
# extra); git is useful for hatch/pre-commit workflows inside the container.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install metadata resolves during
# the dependency-only layer, then install all runtime + dev dependencies.
RUN mkdir -p src/strands && touch src/strands/__init__.py \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy the full project source.
COPY . .

# Reinstall editable to pick up real package metadata/entry points.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
