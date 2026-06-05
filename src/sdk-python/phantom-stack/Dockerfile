FROM python:3.10-slim

# hatch-vcs derives the version from git tags. There is no git history in the
# build context, so provide a pretend version for setuptools_scm/hatch-vcs.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

# System deps:
#  - git: hatch-vcs/setuptools_scm probes for a repo
#  - build-essential: for any native wheels that need compilation
#  - portaudio19-dev / libasound2-dev: audio deps used by optional bidi feature
#    (matches the project's CI setup)
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-fetch dependencies based on the manifest (editable install of full source
# happens after source copy). Install the project with all + dev extras.
COPY src ./src

RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
