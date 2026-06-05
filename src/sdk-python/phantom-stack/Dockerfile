FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree has no .git,
# so we provide a static version through setuptools_scm's pretend-version hook.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1
ENV PYTHONUNBUFFERED=1

# git is required by hatch-vcs / setuptools_scm even with a pretend version;
# build-essential covers any optional C-extension builds among the deps.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Layer caching: install dependencies based on the manifest before copying source.
COPY pyproject.toml README.md ./

# Create a minimal package + git context so the editable build resolves metadata
# and dependencies during the dependency-install layer.
RUN mkdir -p src/strands \
    && touch src/strands/__init__.py \
    && git init -q \
    && git config user.email build@local \
    && git config user.name build \
    && git add -A \
    && git commit -qm init \
    && git tag v0.0.0 \
    && pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the full source tree (overwrites the placeholder package).
COPY . .

# Re-run editable install so console scripts / metadata reflect the real source.
RUN git add -A 2>/dev/null || true; \
    git commit -qm src 2>/dev/null || true; \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
