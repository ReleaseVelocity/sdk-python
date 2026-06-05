FROM python:3.12-slim

# System deps: git is needed by hatch-vcs to determine version from tags.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Copy the rest of the source.
COPY . /app

# hatch-vcs needs a git repo with a tag to compute the package version.
# The project's .git is not present, so initialize a throwaway repo with a tag.
RUN if [ ! -d /app/.git ]; then \
        git init -q /app && \
        git -C /app config user.email "dev@example.com" && \
        git -C /app config user.name "dev" && \
        git -C /app add -A && \
        git -C /app commit -qm "containerized snapshot" && \
        git -C /app tag v1.0.0 ; \
    fi

# Install the project in editable/development mode with ALL extras + dev/test tooling.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
