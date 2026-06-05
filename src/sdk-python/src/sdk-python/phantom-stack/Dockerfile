FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no .git in this build
# context, so provide a pretend version (honored by setuptools-scm/hatch-vcs).
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION}
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so editable install can resolve the package.
COPY src ./src

# Install the project in editable/development mode with all extras plus dev tools.
RUN pip install --upgrade pip \
    && pip install -e ".[all]" \
    && pip install -e ".[dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
