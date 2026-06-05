FROM python:3.12-slim

# portaudio is needed for the optional bidi extra (pyaudio); git needed for hatch-vcs versioning
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
        libffi-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy project metadata first for better layer caching
COPY pyproject.toml README.md ./

# hatch-vcs derives the version from git tags; provide a fallback so the build
# does not fail when no git history/tags are present.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0

# Copy the source so editable install can resolve the package
COPY src ./src

# Install the project in editable/development mode with ALL extras plus dev tooling.
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
