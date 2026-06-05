# Strands Agents Python SDK - development image
# Python 3.12 supports the full feature matrix (project requires >=3.10)
FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is needed at build time: the project derives its version from VCS tags
# via hatch-vcs (see [tool.hatch.version] source = "vcs" in pyproject.toml).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to leverage Docker layer caching.
COPY pyproject.toml README.md ./

# Copy the rest of the source tree (needed for editable install of src/strands).
COPY . /app

# Provide a deterministic VCS version so hatch-vcs does not fail in the absence
# of a real git history. We initialise a throwaway git repo with a tag.
RUN if [ ! -d /app/.git ]; then \
        git init -q /app && \
        git -C /app config user.email "dev@example.com" && \
        git -C /app config user.name "dev" && \
        git -C /app add -A && \
        git -C /app commit -qm "dev snapshot" && \
        git -C /app tag v0.0.0-dev ; \
    fi

# Install the project in editable/development mode with ALL optional model
# providers plus the dev/test tooling (pytest, ruff, mypy, moto, hatch, ...).
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
