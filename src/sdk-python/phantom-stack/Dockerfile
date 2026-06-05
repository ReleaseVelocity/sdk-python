FROM python:3.12-slim

# hatch-vcs derives version from git tags; the source tree here has no git
# history, so provide a pretend version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# git is required by hatch-vcs version source even with the pretend version,
# and is generally useful for the dev workflow (pre-commit, commitizen).
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install the project in editable/development mode with ALL optional deps,
# the dev toolchain, and docs extras so a developer can run tests + linters.
# Copy the rest of the source after deps resolution.
COPY . .

RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
