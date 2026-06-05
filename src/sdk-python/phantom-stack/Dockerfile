FROM python:3.11-slim

# hatch-vcs determines version from git tags. Since the build context may not
# contain a .git directory, provide a pretend version so the build doesn't fail.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is needed by hatch-vcs; build tools occasionally needed by deps (pyaudio etc. are not in default install)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Initialize a git repo so hatch-vcs has something to work with, then install.
# Install the project in editable/development mode with all + dev dependencies.
COPY src ./src

RUN git init -q && git add -A && git -c user.email=dev@example.com -c user.name=dev commit -qm "init" \
    && git tag v0.0.0 \
    && pip install -e ".[all,dev]"

# Copy the remaining source (tests, configs, etc.)
COPY . .

CMD ["sleep", "infinity"]
