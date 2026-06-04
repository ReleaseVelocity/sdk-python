FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context has no
# .git, provide a pretend version so the build backend succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# portaudio is needed for pyaudio (bidi optional dep). git is useful for dev.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-install dependencies (without the project source) to leverage cache.
# Create a minimal package stub so the editable build resolves the package dir.
RUN mkdir -p src/strands && touch src/strands/__init__.py \
    && pip install -e ".[all,dev]" \
    && rm -rf src

# Copy the full project source.
COPY . .

# Reinstall in editable mode now that real source is present.
RUN pip install -e ".[all,dev]"

CMD ["bash"]
