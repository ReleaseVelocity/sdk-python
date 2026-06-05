FROM python:3.13-slim

# hatch-vcs derives the version from git tags. The build context has no git
# history, so provide a pretend version that setuptools_scm/hatch-vcs honor.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is needed by hatch-vcs / pre-commit tooling; build-essential only if a
# wheel requires compilation (kept minimal).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Layer caching: copy only the dependency manifest first.
COPY pyproject.toml README.md ./

# Create the package layout so editable install resolves, then install all
# runtime + provider + dev/test dependencies.
RUN mkdir -p src/strands && touch src/strands/__init__.py \
    && pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the full source tree (real package + tests).
COPY . .

# Reinstall editable so metadata points at the real sources after copy.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
