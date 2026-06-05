FROM python:3.12-slim

# hatch-vcs derives the version from git tags. We provide a pretend version so
# the build does not require a populated git history.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is needed by hatch-vcs / pre-commit; portaudio for the optional pyaudio dep
# is not installed since 'bidi' extras are not part of the default dev install.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./
# Source package is required by an editable install (hatch reads packages = src/strands).
COPY src ./src

# Install the project in editable/dev mode with ALL optional + dev dependencies.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
