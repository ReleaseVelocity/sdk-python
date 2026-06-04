FROM python:3.12-slim

# hatch-vcs derives version from git tags; the build context has no .git,
# so provide a pretend version to the hatch-vcs build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install the project in editable/development mode with ALL extras + dev/test deps.
# Copy full source then install so editable install resolves package metadata.
COPY . .

RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
