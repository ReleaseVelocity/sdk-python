FROM python:3.11-slim

# hatch-vcs derives the version from git tags. The workspace is not a git repo,
# so provide a pretend version to allow the build backend to succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# Some optional deps (e.g. pyaudio under bidi) need build tools / portaudio.
# We install the standard "all" + "dev" extras which do not require portaudio,
# but keep gcc available for any source builds.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Pre-install dependencies. The package itself is installed editable later once
# full source is copied. We init a throwaway git repo so hatch-vcs is happy too.
COPY . /app

# Provide a minimal git history so hatch-vcs can resolve a version even if
# the pretend-version env is unset by a consumer.
RUN git init -q && git add -A && \
    git -c user.email=dev@example.com -c user.name=dev commit -qm init && \
    git tag v0.0.0

# Install the project in editable/dev mode with all optional providers + dev tools.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
