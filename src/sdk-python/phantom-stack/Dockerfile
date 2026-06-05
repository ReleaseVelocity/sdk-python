FROM python:3.12-slim

# Version for hatch-vcs (hatchling) since the build context has no .git history.
# hatch-vcs uses setuptools_scm under the hood, which honors this env var.
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION}

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./
RUN pip install --no-cache-dir --upgrade pip

# Copy the full source tree.
COPY . /app

# Install the project in editable/development mode with ALL optional + dev deps.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
