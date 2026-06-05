FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# contain git metadata, provide an explicit fallback version that hatch-vcs /
# setuptools-scm honor. Overridable via build arg.
ARG PKG_VERSION=0.1.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Copy the source package so the editable install resolves the package layout.
COPY src ./src

# Install the project in editable/development mode with ALL optional extras
# plus the dev/test tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Bring in the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
