FROM python:3.13-slim

# System deps:
# - git: hatch-vcs reads git tags to compute the package version (dynamic version)
# - portaudio19-dev + gcc: needed to build pyaudio (bidi extra)
# - build-essential: for any native wheels lacking prebuilt binaries
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    build-essential \
    portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy the project source.
COPY . .

# hatch-vcs computes the package version from git tags. This checkout has no
# git metadata, so initialize a throwaway repo with a version tag.
RUN git config --global --add safe.directory /app \
    && git init -q \
    && git add -A \
    && git -c user.email=build@docker -c user.name=docker commit -q -m "docker build" \
    && git tag v1.0.0

# Install the project in editable/development mode with ALL extras + dev tooling.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
