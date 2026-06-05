FROM python:3.12-slim

# Some optional deps (e.g. pyaudio is excluded here, but litellm/others may
# need a compiler). Install minimal build tooling and git (hatch-vcs needs git).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git tags; provide a fallback so the build
# does not fail when the source tree has no tags / git history.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the source so the editable install can find the package, then install
# the project in development mode with all optional + dev dependencies.
COPY src ./src

RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
