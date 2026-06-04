FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include git history, provide a pretend version so the editable install works.
ARG HATCH_VCS_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION}
# hatch-vcs delegates to setuptools-scm; provide its pretend-version vars too.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION}
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${HATCH_VCS_PRETEND_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# System dependencies:
#  - portaudio19-dev + gcc: required to build pyaudio (bidi extra)
#  - git: hatch tooling may invoke it
WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

# Layer caching: copy dependency manifest + sources required for the editable
# install before the rest of the tree.
COPY pyproject.toml README.md ./
COPY src ./src

# Install the project in editable/development mode with ALL dependencies
# (runtime extras + dev/test tooling + bidi extras).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev,bidi-all]"

# Now copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["bash"]
