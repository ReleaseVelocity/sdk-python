FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to satisfy the dynamic version build hook.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev

# portaudio is needed for the optional pyaudio (bidi) dependency; build tools
# allow native wheels to compile if a prebuilt wheel is unavailable.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy manifest first for better layer caching of dependency installs.
COPY pyproject.toml README.md ./

# Copy the package source so editable install can resolve the package.
COPY src ./src

# Install the project in editable/development mode with all + dev dependencies.
RUN pip install --upgrade pip \
    && pip install -e ".[dev,all]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["python", "-c", "import strands; print('strands ok')"]
