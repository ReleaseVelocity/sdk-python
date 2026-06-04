FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The workspace copy has no .git,
# so provide a pretend version to setuptools_scm (used by hatch-vcs).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# PortAudio headers/libs are needed to build pyaudio (pulled in by the bidi extras).
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the project is installed editable and
# the build backend references src/strands).
COPY src ./src

# Install the project in editable/development mode with ALL runtime extras plus
# the dev/test tooling so a developer can run tests and linters in the container.
RUN pip install --no-cache-dir -e ".[all,bidi-all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
