FROM python:3.13-slim

# hatch-vcs derives the version from git tags. The build context has no git
# history, so provide a pretend version (honored by setuptools-scm/hatch-vcs).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install the project in editable mode with ALL optional deps + dev tooling.
# 'all' provides every model provider (anthropic, openai, litellm, gemini,
# ollama, mistral, writer, llamaapi, sagemaker, a2a, otel) so the full test
# suite can be collected and run. 'dev' adds pytest, ruff, mypy, moto, etc.
COPY . .
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
