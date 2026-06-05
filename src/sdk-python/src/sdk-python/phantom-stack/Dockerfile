FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no git history in the
# build context, so provide a pretend version (consumed by setuptools_scm,
# which hatch-vcs uses under the hood).
ARG PKG_VERSION=1.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION}

# System deps:
#  - git: hatch-vcs build backend invokes git at build time
#  - build-essential: some transitive wheels may need compilation
#  - portaudio19-dev: pyaudio (pulled in by the bidi extra) needs PortAudio headers
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Layer caching: copy only the build manifest first so dependency resolution
# is cached independently of source changes.
COPY pyproject.toml README.md ./

# The wheel target only packages src/strands; create a placeholder so the
# editable install can resolve the package layout before full source copy.
COPY src/strands ./src/strands

# Install in editable/development mode with ALL optional features plus the dev
# (test/lint) tooling so a developer can run tests and linters in-container.
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

# Now copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
