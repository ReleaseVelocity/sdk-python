FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context may not
# contain git history, so provide a pretend version to allow the build.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# Some optional deps (e.g. pyaudio for the bidi extra) need build tools and
# portaudio headers. Install git so hatch-vcs/hatch behave normally too.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-install dependencies so source changes don't force a full reinstall.
# Editable install requires the package source, so create a stub package dir.
RUN mkdir -p src/strands && touch src/strands/__init__.py \
    && pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Now copy the full project source.
COPY . .

# Re-run the editable install against the real source tree so all entry
# points / metadata reflect the actual package.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
