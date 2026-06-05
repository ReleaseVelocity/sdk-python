FROM python:3.12-slim

# hatch-vcs derives the version from git tags; since the build context has no
# .git history, provide a pretend version so the build backend succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# portaudio is required to build pyaudio (used by the optional bidi extra).
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching. README.md is
# referenced by pyproject (readme field) so it is needed at install time.
COPY pyproject.toml README.md ./
COPY src/ ./src/

# Install the project in editable/development mode with all runtime extras
# plus the dev/test tooling so a developer can run tests and linters.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in the remaining source (tests, docs, configs).
COPY . .

CMD ["sleep", "infinity"]
