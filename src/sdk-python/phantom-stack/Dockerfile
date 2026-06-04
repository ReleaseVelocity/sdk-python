FROM python:3.12-slim

# hatch-vcs (setuptools_scm) determines version from git tags. Since the build
# context has no git repo, provide a fallback version so editable install works.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is needed because the project uses hatch-vcs for versioning; build tools
# are kept minimal and only added if native builds require them.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the rest of the source.
COPY . .

# Initialize a git repo with a tag so hatch-vcs can resolve a version,
# and install the project in editable/dev mode with ALL optional deps,
# dev tooling, and the bidi-all extras so every test can be collected.
RUN git init -q && git add -A && \
    git -c user.email=dev@example.com -c user.name=dev commit -qm "init" && \
    git tag v0.0.0 && \
    pip install --upgrade pip && \
    pip install -e ".[all,dev,bidi-all]"

CMD ["sleep", "infinity"]
