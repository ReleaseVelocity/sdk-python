FROM python:3.12-slim

# hatch-vcs (setuptools_scm) reads the version from git tags. The source tree
# has no git history in this environment, so provide a pretend version so the
# editable build succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-install the project's declared dependencies (dev + all extras) using the
# manifest before copying full source. We create a minimal package stub so the
# editable build target exists, then we overwrite with the real source later.
RUN mkdir -p src/strands && touch src/strands/__init__.py \
    && pip install --no-cache-dir -e ".[all,dev]"

# Now copy the real source and tests over the stub.
COPY . .

# Re-run the editable install so metadata/entry points reflect the full source.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["tail", "-f", "/dev/null"]
