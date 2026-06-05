FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is needed because the project uses hatch-vcs (version from git tags)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy the full project source.
COPY . /app

# hatch-vcs derives the version from git tags. Ensure there is a git repo with a
# tag so the build does not fail when the project is copied without .git.
RUN if [ ! -d /app/.git ]; then \
        git init -q /app && \
        git -C /app add -A && \
        git -C /app -c user.email=dev@example.com -c user.name=dev commit -qm init && \
        git -C /app tag v0.0.0 ; \
    fi

# Install the project in editable/dev mode with ALL extras + dev/test tooling.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
