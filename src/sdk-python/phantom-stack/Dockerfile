FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the source tree may not
# include git metadata, provide a pretend version so the build backend succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# Some optional dependencies (e.g. pyaudio for the bidi extras) require native
# build tooling and PortAudio headers. Install them so the full extra set builds.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy the whole project (small pure-Python SDK). Build context is the project
# root (see docker-compose.yaml build.context).
COPY . .

# Initialize a git repo + tag so hatch-vcs/setuptools_scm can resolve a version,
# then install the project in editable/development mode with ALL extras plus the
# dev/test tooling so tests, linters, and type checks are all available.
RUN git init -q && git add -A && \
    git -c user.email=dev@example.com -c user.name=dev commit -qm "init" && \
    git tag v0.0.0 && \
    pip install --no-cache-dir -e ".[all,dev]"

CMD ["python", "-c", "import strands; print('strands import OK')"]
