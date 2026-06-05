FROM python:3.12-slim

# Avoid interactive prompts and enable predictable Python behavior
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# System deps:
#  - git: required because the build backend (hatch-vcs) derives the package
#    version from git tags
#  - build-essential, portaudio19-dev: needed to build native wheels such as
#    pyaudio (pulled in by the optional "bidi" extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy only the dependency manifest first to leverage layer caching.
COPY pyproject.toml ./

# hatch-vcs requires a git repo with a version tag to resolve the dynamic
# version. The source tree shipped here has no .git, so we seed a minimal repo
# with a tag. SETUPTOOLS/HATCH_VCS will then resolve a valid version.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# Copy the rest of the project source.
COPY . .

# Initialize a git repo + tag so hatch-vcs can build the editable install even
# if PRETEND_VERSION is not honored by every code path.
RUN git config --global user.email "dev@example.com" \
    && git config --global user.name "dev" \
    && git init -q \
    && git add -A \
    && git commit -q -m "container build snapshot" \
    && git tag v0.0.0

# Install the project in editable/development mode with ALL dependencies,
# including every model-provider extra ([all]) and the dev/test tooling ([dev]).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
