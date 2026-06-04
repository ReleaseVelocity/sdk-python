FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no git
# history, so provide a pretend version for the package (normalized name).
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is referenced by hatch-vcs build backend; portaudio headers are needed
# only if the optional bidi audio extra is later installed.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifests first for better layer caching.
COPY pyproject.toml README.md ./

# Initialize a throwaway git repo so hatch-vcs can resolve a version, then
# install the project in editable/development mode with all + dev extras.
COPY src ./src
RUN git init -q && git add -A && \
    git -c user.email=build@local -c user.name=build commit -qm "build" && \
    git tag v0.0.0 && \
    pip install -e ".[dev,all]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["bash"]
