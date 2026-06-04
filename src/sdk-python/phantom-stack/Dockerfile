FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include git metadata, provide a pretend version so the build/install succeeds.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0

WORKDIR /app

# Minimal build tooling; most wheels are prebuilt but keep git for VCS-aware tooling.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

RUN pip install --no-cache-dir --upgrade pip

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source so the editable install can find the package.
COPY src ./src

# Install the project in editable/development mode with ALL feature extras
# plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs).
COPY . .

CMD ["sleep", "infinity"]
