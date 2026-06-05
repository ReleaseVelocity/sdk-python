FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version that setuptools-scm / hatch-vcs will honor.
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION}

# Minimal build tooling. Most deps ship wheels; git is useful for dev workflows.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

RUN pip install --no-cache-dir --upgrade pip

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Bring in the package sources needed for an editable install.
COPY src ./src

# Install the project in editable/dev mode with ALL optional + dev dependencies.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Now copy the rest of the project (tests, docs, configs).
COPY . .

CMD ["sleep", "infinity"]
