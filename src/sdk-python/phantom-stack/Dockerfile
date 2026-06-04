FROM python:3.12-slim

# Avoid interactive prompts and enable clean Python output
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1

# git is required because the project version is derived from git tags (hatch-vcs)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency/build manifests first for better layer caching
COPY pyproject.toml README.md ./

# hatch-vcs reads the version from git tags. The source tree may not include
# git metadata, so provide a fallback version so the build/install does not fail.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# Copy the full source so the editable install resolves the package layout
COPY . .

# Initialize a git repo with a tag if none exists so hatch-vcs can resolve a version.
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -qm "phantom-stack build" && \
        git tag v0.0.0 ; \
    fi

# Install the project in editable/development mode with all dev + provider deps.
RUN pip install --upgrade pip && \
    pip install -e ".[dev,all]"

CMD ["sleep", "infinity"]
