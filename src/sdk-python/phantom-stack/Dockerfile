FROM python:3.13-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# git is needed because the package version is derived from VCS (hatch-vcs)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the rest of the project
COPY . .

# hatch-vcs needs a git repo with at least one tag to determine the version.
# The exported source has no .git, so initialize one with a fallback tag.
RUN git rev-parse --git-dir >/dev/null 2>&1 || \
    (git init -q && git add -A && \
     git -c user.email=dev@local -c user.name=dev commit -qm init && \
     git tag v0.0.0)

# Install the project in editable/dev mode with ALL optional deps + dev tools
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
