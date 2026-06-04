FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Provide a pretend version so an
# editable install works without a real git history/tag present in the build.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Minimal system deps. git is used by hatch-vcs; build tooling kept light.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        curl \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the source package so the editable install can resolve the package.
COPY src ./src

# Initialize a throwaway git repo with a tag so hatch-vcs has a clean version,
# then install the project in editable/development mode with ALL extras + dev
# tooling (pytest, ruff, mypy, moto, etc.).
RUN git init -q && git add -A && \
    git -c user.email=dev@example.com -c user.name=dev commit -qm init && \
    git tag v1.0.0 && \
    pip install --no-cache-dir -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
