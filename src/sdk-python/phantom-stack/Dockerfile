FROM python:3.11-slim

# hatch-vcs determines the version from git tags. Since the build context may not
# contain a .git directory, provide a pretend version (overridable via build arg).
ARG STRANDS_VERSION=1.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION}
ENV PYTHONUNBUFFERED=1
ENV PIP_DISABLE_PIP_VERSION_CHECK=1

# git is needed by hatch-vcs build backend; portaudio dev headers needed for pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy source needed for editable install metadata
COPY src ./src

# Install the project in editable/dev mode with all optional deps + dev/test tools.
# Initialize a minimal git repo so hatch-vcs has a clean state to work with.
RUN git init -q && git add -A && \
    git -c user.email=dev@local -c user.name=dev commit -qm init && \
    git tag v${STRANDS_VERSION} && \
    pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
