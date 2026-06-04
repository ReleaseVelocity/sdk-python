FROM python:3.12-slim

# hatch-vcs derives version from git tags; without a git repo we must provide a
# pretend version so the editable build can succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# portaudio is required to build pyaudio (bidi extra). git is handy for tooling.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Editable install with the full set of extras + dev/test tooling.
# Create the package dir so editable install resolves before full source copy.
RUN mkdir -p src/strands && touch src/strands/__init__.py \
    && pip install -e ".[all,dev]"

# Now copy the real source tree.
COPY . .

# Reinstall editable to pick up real package metadata/entry points.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
