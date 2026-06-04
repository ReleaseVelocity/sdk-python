FROM python:3.13-slim

# System deps: git is required by hatch-vcs to determine version from tags;
# portaudio is needed for pyaudio (bidi extras) if ever installed.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for editable install / version detection.
COPY src ./src

# The project uses hatch-vcs (git tags) for versioning. There is no git
# history in the build context, so provide the version explicitly.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0

RUN pip install --upgrade pip hatch hatch-vcs && \
    pip install -e ".[dev,all]"

# Copy the rest of the project (tests, configs, etc.)
COPY . .

CMD ["sleep", "infinity"]
