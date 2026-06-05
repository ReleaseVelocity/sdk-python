FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context has no
# git repo/tags, provide a pretend version so the build backend can resolve it.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# portaudio is required to build/run the optional pyaudio (bidi) dependency.
# gcc/build tools cover any source-only wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml ./

# Copy the rest of the project.
COPY . .

# Install in editable/development mode with all features + dev/test deps.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
