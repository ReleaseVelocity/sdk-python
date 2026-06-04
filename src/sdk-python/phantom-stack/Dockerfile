FROM python:3.13-slim

# setuptools_scm/hatch-vcs needs a version since there is no git history in the build context.
# This is wired through a build arg so different environments can override it.
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION}

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Upgrade pip tooling.
RUN pip install --no-cache-dir --upgrade pip setuptools wheel

# Copy the full source (needed for editable install of the package).
COPY . .

# Install the project in editable/development mode with ALL optional deps + dev/test tooling.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
