FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the source is shipped
# without git history, provide a pretend version so the build/install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# portaudio is required by pyaudio (pulled in by the bidi optional deps).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install / hatch build hooks).
COPY src ./src

# Install the project in editable/development mode with ALL optional groups
# plus the dev/test tooling so a developer can run tests and linters.
RUN pip install --upgrade pip && \
    pip install -e ".[all,bidi-all,dev]"

# Copy the remainder of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
