FROM python:3.12-slim

# Tell hatch-vcs / setuptools_scm a version since this build has no git tags
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+docker
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is needed by hatch-vcs versioning; portaudio dev headers for pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Initialize a minimal git repo so hatch-vcs has a tag to derive the version from
RUN git init -q && git config user.email "dev@local" && git config user.name "dev" \
    && git add -A && git commit -qm "init" && git tag v0.0.0

# Copy the package source needed for editable install
COPY src ./src

# Install the project in editable/development mode with ALL extras + dev tooling
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
