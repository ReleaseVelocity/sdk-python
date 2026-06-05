FROM python:3.12-slim

# hatch-vcs (setuptools_scm) needs a version; no .git is present in the build context,
# so we provide a pretend version to allow the editable install to succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

# git is useful for dev workflows; build-essential covers any native builds (e.g. pyaudio not installed here)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source so the editable install can resolve the package
COPY src ./src

# Install the project in editable/development mode with all optional + dev/test deps
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
