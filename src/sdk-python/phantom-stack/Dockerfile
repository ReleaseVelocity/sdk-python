FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# contain git metadata, provide a fallback version via setuptools_scm.
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# Build tooling for any packages that need to compile native extensions.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install of strands).
COPY src/strands ./src/strands

# Install the project in editable/development mode with dev tooling and all
# optional model-provider extras so the full test suite can be collected/run.
RUN pip install --upgrade pip \
    && pip install -e ".[dev,all]"

# Copy the remaining project sources (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
