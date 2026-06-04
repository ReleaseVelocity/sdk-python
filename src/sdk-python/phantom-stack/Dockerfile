FROM python:3.12-slim

# git: required by hatch-vcs to derive the package version at build time.
# build-essential: available in case an optional dependency needs to compile.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

RUN pip install --upgrade pip setuptools wheel

# Copy the full project. hatch-vcs needs the source tree present to build.
COPY . .

# hatch-vcs reads the package version from git tags. The source tree ships
# without a .git directory, so create a throwaway repo + tag to provide one.
RUN git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -q -m "containerized build" \
    && git tag v0.0.0

# Install in editable/development mode with ALL optional features plus the
# dev/test toolchain (pytest, pytest-asyncio, pytest-xdist, ruff, mypy, moto).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
