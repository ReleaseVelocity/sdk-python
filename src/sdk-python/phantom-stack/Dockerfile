FROM python:3.12-slim

# portaudio is needed for pyaudio (bidi extra); git for hatch-vcs versioning
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        libc6-dev \
        portaudio19-dev \
        libportaudio2 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git tags. Provide a pretend version so the
# build does not require a tagged git history.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install.
COPY src ./src

# Initialize a minimal git repo so hatch-vcs has something to work with as a
# fallback, then install the project in editable/development mode with ALL
# optional dependency groups plus dev/test tooling.
RUN git init -q && git add -A && git -c user.email=a@b.c -c user.name=build commit -qm init \
    && git tag v0.0.0 \
    && pip install --no-cache-dir -e ".[all,bidi,bidi-gemini,bidi-openai,dev]"

# Bring in the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
