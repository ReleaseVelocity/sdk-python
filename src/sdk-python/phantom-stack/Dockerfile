FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the source is shipped
# without a .git directory, provide a pretend version so the build succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# portaudio is required to build pyaudio (pulled in by some optional extras).
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install / version resolution).
COPY src ./src

# Install the project in editable/development mode with all extras + dev tools.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
