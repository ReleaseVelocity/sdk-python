FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to let the editable install succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

# Install build/runtime tooling. portaudio is needed for pyaudio (bidi extra),
# git is referenced by hatch-vcs/pre-commit workflows.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so hatchling can resolve packages during editable install.
COPY src ./src

# Install the project in editable/development mode with ALL extras + dev/test deps.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the source (tests, configs, etc.)
COPY . .

CMD ["sleep", "infinity"]
