FROM python:3.13-slim

# Avoid interactive prompts and keep Python output unbuffered
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to allow the editable build to succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0.dev0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0.dev0

# Minimal build tooling. Most wheels are prebuilt; gcc/g++ cover any source builds.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        build-essential \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install build).
COPY src ./src

# Install the project in editable/development mode with dev tools and all
# optional model-provider extras. Note: the "bidi" extra is intentionally
# excluded because it requires native PortAudio (pyaudio) and is not part of "all".
RUN pip install --upgrade pip \
    && pip install -e ".[dev,all]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["bash"]
