FROM python:3.12-slim

# hatch-vcs derives the package version from git tags. The build context may not
# contain a .git directory, so initialize a minimal repo with a tag to provide a
# valid version. Override the version with --build-arg PKG_VERSION=x.y.z
ARG PKG_VERSION=0.0.0

WORKDIR /app

# git is required by hatch-vcs to detect the version.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

# Copy the full project. hatch-vcs needs the source tree present to build.
COPY . .

# Provide version metadata: reuse an existing .git if present, otherwise create
# a throwaway repo with a tag so hatch-vcs can resolve the version.
RUN if [ ! -d .git ]; then \
        git init -q \
        && git config user.email "dev@example.com" \
        && git config user.name "dev" \
        && git add -A \
        && git commit -qm "containerize" \
        && git tag "v${PKG_VERSION}"; \
    fi

# Install the project in editable/development mode with ALL optional features
# plus dev/test tooling (pytest, moto, ruff, mypy, etc.).
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
