FROM python:3.12-slim

# hatch-vcs determines the version from git tags. Since the source tree may not
# include a .git directory, provide a pretend version so the build succeeds.
ARG STRANDS_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION}
ENV PIP_NO_CACHE_DIR=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the source package (needed for editable install / metadata build).
COPY src ./src

# Install the project in editable/dev mode with all optional + dev dependencies.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
