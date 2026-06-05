FROM python:3.12-slim

# portaudio is required to build pyaudio (bidi extra); git is needed because the
# build backend (hatch-vcs) inspects version control, though we also pretend a version.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
        jq \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs (setuptools_scm) determines the version from git tags. The source here
# has no VCS history, so provide a pretend version to make the editable build succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install can resolve packages during
# the manifest-only layer; full source is copied next.
COPY src/strands/ ./src/strands/

# Install the project in editable/development mode with ALL runtime extras plus
# the dev (test/lint) tools. This mirrors the hatch-test/default env feature set.
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining source (tests, configs, docs).
COPY . .

CMD ["bash"]
