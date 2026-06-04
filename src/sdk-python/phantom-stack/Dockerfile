FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to let the editable install succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is occasionally needed by hatch-vcs/tooling; install minimally.
RUN apt-get update && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Leverage layer caching: copy only the manifest + sources required for the
# editable install metadata first.
COPY pyproject.toml README.md ./

# Install all runtime + optional provider deps + dev/test tooling in editable mode.
# Copy the package source so the editable install can resolve the package.
COPY src ./src
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
