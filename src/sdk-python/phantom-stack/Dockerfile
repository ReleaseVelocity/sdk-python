FROM python:3.13-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include git metadata, provide a pretend version (consumed by setuptools_scm,
# which hatch-vcs wraps).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+docker \
    HATCH_VCS_PRETEND_VERSION=0.0.0+docker \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# git is useful for hatch-vcs / pre-commit; build tools occasionally needed
# for native wheels (e.g. pyaudio is intentionally excluded via the bidi extra).
RUN apt-get update && \
    apt-get install -y --no-install-recommends git && \
    rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the build backend references src/strands)
COPY src ./src

# Install the project in editable/development mode with all optional + dev deps.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
