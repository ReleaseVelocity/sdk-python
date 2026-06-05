FROM python:3.12-slim

# hatch-vcs needs a version. Provide a pretend version so the build does not
# require real git tags (also covers the case where source is not a git repo).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the source package (needed for an editable install to resolve).
COPY src ./src

# Install the project in editable/development mode with ALL extras + dev tooling.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
