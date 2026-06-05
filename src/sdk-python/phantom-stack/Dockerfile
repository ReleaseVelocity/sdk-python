FROM python:3.13-slim

# pyaudio (bidi extra) needs portaudio headers; build tools for any native deps.
# git is required because hatch-vcs determines the version from git history.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Leverage layer caching: copy only the build manifest first.
COPY pyproject.toml ./
COPY README.md ./

# hatch-vcs needs a git repo with at least one tag to compute the version.
# Initialize a throwaway repo and tag so the editable install succeeds even
# when the build context has no .git directory.
RUN pip install --no-cache-dir --upgrade pip

# Copy the full source tree (needed for editable install of the package).
COPY . /app

# Ensure a git repo with a tag exists for hatch-vcs version resolution.
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -qm "containerize" && \
        git tag v0.0.0 ; \
    fi

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain (pytest, moto, ruff, mypy, ...).
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
