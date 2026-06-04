FROM python:3.13-slim

# hatch-vcs derives the version from git tags. There is no .git here, so we
# provide a pretend version to let the editable build/install succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# portaudio is required to build pyaudio (pulled in by the bidi extra).
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        g++ \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Pre-create package dir so editable install metadata resolves during dep step.
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install all runtime + optional + dev dependencies (editable, dev mode).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the source tree.
COPY . .

# Re-run editable install so console scripts / metadata reflect the full source.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
