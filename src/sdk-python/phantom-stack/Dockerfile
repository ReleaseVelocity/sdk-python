FROM python:3.12-slim

# hatch-vcs / setuptools_scm needs a version since there is no .git in the build context.
# Provide a pretend version so the editable install can compute the package version.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is useful for dev workflows; build tools generally not required for these wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because editable install references src/strands).
COPY src ./src

# Install the project in editable/development mode with ALL extras + dev/test tools.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
