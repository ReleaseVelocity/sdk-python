FROM python:3.12-slim

# hatch-vcs determines the version from git tags. Since the source is shipped
# without a git history, provide a pretend version so the build backend can
# resolve a version without needing tags.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

# System packages:
#  - git: hatch-vcs queries git (kept available as a fallback)
#  - build-essential: native builds for some optional deps
#  - portaudio19-dev: required to build pyaudio (bidi optional feature)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the install is editable and
# hatchling reads packages = ["src/strands"]).
COPY src ./src

# Install the project in development/editable mode with every optional feature
# (all model providers + bidi) plus the dev/test tooling.
RUN pip install --upgrade pip \
    && pip install -e ".[all,bidi,bidi-gemini,bidi-openai,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
