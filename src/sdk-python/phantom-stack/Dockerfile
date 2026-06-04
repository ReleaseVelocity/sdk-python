FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree ships without a
# .git directory, so we initialize a throwaway repo with a tag at build time so
# setuptools-scm/hatch-vcs can resolve a concrete version.
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# git is useful for pre-commit / dev workflows inside the container.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install.
COPY src ./src

# Copy the remaining project files (tests, configs, docs).
COPY . .

# Initialize a git repo with a tag so hatch-vcs/setuptools-scm can resolve a
# concrete version, then install the project in editable/development mode with
# ALL optional dependencies plus the dev/test toolchain (pytest, ruff, mypy,
# moto, hatch...).
RUN git init -q && git config user.email dev@example.com \
    && git config user.name dev \
    && git add -A && git commit -qm "build" \
    && git tag v0.0.0 \
    && pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
