FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to let the editable install succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
# boto3-backed tests need a region/creds; provide harmless defaults for the dev env.
ENV AWS_DEFAULT_REGION=us-east-1 \
    AWS_ACCESS_KEY_ID=testing \
    AWS_SECRET_ACCESS_KEY=testing
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# PyAudio (bidi extra) needs PortAudio headers + a compiler.
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        portaudio19-dev \
        libasound2-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create package dir so the editable install can resolve packages=["src/strands"].
COPY src ./src

# Install the project in editable/development mode with all + dev dependencies.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
