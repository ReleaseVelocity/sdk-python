FROM python:3.12-slim

# Some packages (e.g. pyaudio in bidi extras, native wheels) may need build tools.
# Install minimal build deps only.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git tags. When building from a copied
# source tree without tags, provide a fallback version so the build succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the full project source.
COPY . .

# Install the project in editable/development mode with all runtime extras
# plus the dev/test tooling so a developer can run tests and linters.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
