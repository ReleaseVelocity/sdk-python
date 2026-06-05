FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no git
# history, so provide a pretend version (consumed by setuptools_scm/hatch-vcs).
ARG PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PRETEND_VERSION} \
    HATCH_VCS_PRETEND_VERSION=${PRETEND_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# System deps:
#  - portaudio19-dev: required to build pyaudio (bidi extra)
#  - git: hatch/commitizen and editable VCS-based tooling expect it present
#  - build-essential: native wheels (pyaudio) need a compiler
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
        curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-resolve dependencies using the manifest. We create a minimal package
# tree so the editable install in the next step is fast and cached.
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install the project in editable/development mode with the full feature set
# plus dev/test tooling. bidi-all pulls in audio/realtime extras.
RUN pip install --no-cache-dir -e ".[all,dev,bidi-all]"

# Now copy the full source tree (overrides the placeholder package).
COPY . .

# Ensure editable metadata reflects the real package after full copy.
RUN pip install --no-cache-dir --no-deps -e .

CMD ["python", "-c", "import strands; print('strands', getattr(strands, '__version__', 'ok'))"]
