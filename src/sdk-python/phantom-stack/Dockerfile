FROM python:3.13-slim

# System deps:
# - git: required by hatch-vcs to determine the package version from tags
# - build-essential + portaudio19-dev: needed to build pyaudio (bidi extras)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy the whole project (build context is the project root)
COPY . .

# hatch-vcs derives the version from git tags. The source tree may not contain
# a .git directory, so initialize a minimal repo with a tag and also provide a
# pretend-version fallback. This keeps the version configurable yet reproducible.
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && git commit -qm "snapshot" && \
        git tag v0.0.0; \
    fi

# Install the project in editable/development mode with ALL runtime extras
# plus dev/test tooling, matching the hatch test env (features = ["all"]).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
