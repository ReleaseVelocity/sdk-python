FROM python:3.13-slim

# hatch-vcs (setuptools_scm) needs a version; repo has no git history in the
# build context, so provide a pretend version via build arg.
ARG PKG_VERSION=1.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# System libraries:
#  - portaudio19-dev + libasound2 are required to build/use pyaudio (bidi extra)
#  - git is used by hatch-vcs tooling and convenient for developers
#  - build-essential provides a compiler for any sdist that needs it
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        libasound2 \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Layer caching: copy only the manifest first so dependency resolution is cached.
COPY pyproject.toml README.md ./

# Editable install with the full dependency surface:
#   all      -> every model provider + a2a + otel + docs
#   bidi-all -> bidirectional streaming (pyaudio, websockets, etc.)
#   dev      -> pytest, ruff, mypy, moto, hatch, pre-commit, ...
# The src tree is copied next; pip needs it present for the editable install,
# so we copy sources before installing.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

RUN pip install --no-cache-dir -e ".[all,bidi-all,dev]"

# Copy the remainder of the project (docs, configs, etc.)
COPY . .

CMD ["sleep", "infinity"]
