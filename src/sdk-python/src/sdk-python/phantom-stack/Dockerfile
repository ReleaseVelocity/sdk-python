FROM python:3.12-slim

# hatch-vcs/setuptools_scm derives the version from git tags. There is no .git
# in the build context, so provide a pretend version to allow the build to work.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# git is useful for dev workflows / pre-commit; build tools rarely needed but
# some optional deps (e.g. pyaudio under bidi) require system libs. We install
# the "all" feature set which does not pull bidi, so keep the image lean.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install all runtime extras + dev/test tooling in editable mode.
# We create the package dir so the editable install resolves before full source copy.
RUN mkdir -p src/strands && touch src/strands/__init__.py \
    && pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Now copy the full source tree (overrides the placeholder package).
COPY . .

# Re-run editable install to pick up entry points / metadata against real source.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
