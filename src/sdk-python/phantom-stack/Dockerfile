FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context is not a
# git repository, provide a pretend version so the editable install/build works.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy source needed for the editable install
COPY src ./src

# Install the project in editable/development mode with ALL extras + dev/test tools
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
