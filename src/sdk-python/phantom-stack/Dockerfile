FROM python:3.13-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# hatch-vcs derives the version from git tags. Since the build context may not
# contain a git history, pin the version so the build is deterministic and
# requires no git/network access.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# Copy the full source. The project is a src-layout library installed editable,
# so the whole tree is needed for the editable install to resolve packages.
COPY . .

# Install the project in editable/development mode with all runtime extras and
# dev/test tooling. "all" includes every model provider; "dev" adds pytest,
# ruff, mypy, moto, etc. (bidi extra is intentionally excluded: it needs
# portaudio/pyaudio and its tests are ignored by the pytest config.)
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
