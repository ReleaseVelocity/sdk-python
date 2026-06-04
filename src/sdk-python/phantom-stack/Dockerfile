FROM python:3.12-slim

# hatch-vcs determines the version from git tags. Since the build context may
# not contain git history, provide a pretend version so the build succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy source so editable install can resolve the package
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the project in editable/development mode with all optional features
# plus the dev/test tooling (pytest, moto, ruff, mypy, etc.)
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
