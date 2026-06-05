FROM python:3.12-slim

# System deps: git (for hatch-vcs), build tools (some deps may compile),
# and portaudio for the optional pyaudio (bidi) dependency.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs determines version from git tags. The source tree has no .git,
# so provide a pretend version for setuptools_scm/hatch-vcs.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0.dev0

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source needed for editable install metadata.
COPY src/strands ./src/strands

# Install the project in editable/dev mode with ALL optional deps + dev + test tooling.
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, etc.)
COPY . .

CMD ["sleep", "infinity"]
