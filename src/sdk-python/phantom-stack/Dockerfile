FROM python:3.13-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include git history, allow the version to be supplied as a build arg and
# expose it via the env var hatch-vcs/setuptools-scm honor.
ARG STRANDS_VERSION=0.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${STRANDS_VERSION}
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION}
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# git is needed by hatch-vcs at build time; build tools are occasionally
# needed for native wheels (e.g. pyaudio in the bidi extra).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        make \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so the editable install can resolve the package.
COPY src ./src

# Install the project in editable/development mode with ALL dependency groups
# (runtime extras + dev/test tooling).
RUN pip install --no-cache-dir -e ".[dev,all,bidi-all]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
