FROM python:3.12-slim

# portaudio is needed for pyaudio (bidi extra); git needed for hatch-vcs versioning
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy manifest first for layer caching
COPY pyproject.toml README.md ./

# Copy the rest of the project
COPY . .

# hatch-vcs derives version from git tags; ensure a git repo + tag exists
RUN git init -q && git add -A && git -c user.email=ci@example.com -c user.name=ci commit -qm "build" && git tag v0.0.0 || true

# Install in editable/dev mode with all + dev dependencies
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["tail", "-f", "/dev/null"]
