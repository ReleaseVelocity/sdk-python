FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is needed: project uses hatch-vcs (version derived from git tags)
RUN apt-get update && \
    apt-get install -y --no-install-recommends git && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for layer caching
COPY pyproject.toml README.md ./

# Copy the rest of the source
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# hatch-vcs needs git metadata to compute the version. Provide it via a
# pretend version env var so the build does not require real git tags.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
