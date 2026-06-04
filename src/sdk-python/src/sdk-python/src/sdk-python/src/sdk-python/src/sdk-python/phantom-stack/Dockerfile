FROM python:3.12-slim

# System deps: git for hatch-vcs introspection (build tooling, light)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs/setuptools_scm derives version from git tags. The source tree has
# no .git directory, so provide a pretend version to allow building/installing.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PIP_NO_CACHE_DIR=1

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable build target exists during the
# dependency resolution step (real source copied below).
RUN mkdir -p src/strands

# Copy full source.
COPY . .

# Install the project in editable/development mode with ALL optional model
# providers ([all]) plus dev/test tooling ([dev]).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
